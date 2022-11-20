```python


@app.route('/')
def main():
    words = list(db.words.find({}, {"_id":False}))
    print(words)
    return render_template("index.html", words=words)

@app.route('/detail/<keyword>')
def detail(keyword):
    print(keyword)
    db_word = db.words.find_one({'word':keyword}, {"_id": False})
    print(db_word)

    word_name = keyword 
    result = None 
    is_saved = False
    examples = [] 

    if db_word:
        if "result" in db_word:
            print('result in db')
            result = db_word["result"]
        else:
            r = requests.get(f"https://owlbot.info/api/v4/dictionary/{keyword}", headers={"Authorization": "Token a637b82188240fdd705a304c813b91d92d01f6ba"})
            result = r.json()

        is_saved=True
        examples = list(db.examples.find({"word":keyword}, {"_id":False}))

    else:
        r = requests.get(f"https://owlbot.info/api/v4/dictionary/{keyword}", headers={"Authorization": "Token a637b82188240fdd705a304c813b91d92d01f6ba"})
        result = r.json()

    return render_template("detail.html", word_name=word_name, result=result, is_saved=is_saved, examples=examples)


@app.route('/api/save_word', methods=["POST"])
def save_word():
    word_receive = request.form["word_give"]
    definition_receive = request.form["definition_give"]

    r = requests.get(f"https://owlbot.info/api/v4/dictionary/{word_receive}", headers={"Authorization": "Token a637b82188240fdd705a304c813b91d92d01f6ba"})
    result = r.json()

    print(word_receive, definition_receive)

    doc = {
        "word":  word_receive,
        "definition" : definition_receive,
        "result": result
    }

    db.words.insert_one(doc)
    return jsonify({"result": "success", "msg": f"word '{word_receive}' saved"})

@app.route('/api/delete_word', methods=["POST"])
def delete_word():
    word_receive = request.form["word_give"]
    db.words.delete_one({"word": word_receive})
    db.examples.delete_many({"word": word_receive})

    return jsonify({"result": "success", "msg": f"word '{word_receive}' deleted"})

@app.route('/api/save_ex', methods=["POST"])
def save_ex():
    word_receive = request.form["word_give"]
    example_receive = request.form["example_give"]
    doc = {
        "word":  word_receive,
        "example" : example_receive
    }
    db.examples.insert_one(doc)
    return jsonify({"result": "success", "msg": f"example '{example_receive}' saved"})

@app.route('/api/delete_ex', methods=["POST"])
def delete_ex():
    word_receive = request.form["word_give"]
    example_receive = request.form["example_give"]
    db.examples.delete_one({
        "word": word_receive,
        "example": example_receive
    })
    return jsonify({"result": "success", "msg": f"example '{example_receive}' deleted"})
```