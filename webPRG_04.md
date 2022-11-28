```python
from flask import Flask, render_template, request, jsonify

app = Flask(__name__)

from pymongo import MongoClient
import certifi
ca = certifi.where()
client = MongoClient('mongodb+srv://test:sparta@cluster0.owfw2bj.mongodb.net/Cluster0?retryWrites=true&w=majority', tlsCAFile=ca)
db = client.dbsparta

@app.route('/')
def home():

    return render_template('index.html')


@app.route("/homework", methods=["POST"])
def homework_post():

    nickname_receive = request.form['nickname_give']
    comment_receive = request.form['comment_give']

    doc = {
        'nickname' : nickname_receive,
        'comment' : comment_receive
    }

    db.fanbook.insert_one(doc)

    return jsonify({'msg': '저장 완료!'})


@app.route("/homework", methods=["GET"])
def homework_get():

    comment_list = list(db.fanbook.find({}, {'_id':False}))

    return jsonify({'fanbooks': comment_list})

if __name__ == '__main__':
    app.run('0.0.0.0', port=5000, debug=True)
```

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">

    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.0.2/dist/css/bootstrap.min.css" rel="stylesheet"
          integrity="sha384-EVSTQN3/azprG1Anm3QDgpJLIm9Nao0Yz1ztcQTwFspd3yD65VohhpuuCOmLASjC" crossorigin="anonymous">
    <script src="https://ajax.googleapis.com/ajax/libs/jquery/3.5.1/jquery.min.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.0.2/dist/js/bootstrap.bundle.min.js"
            integrity="sha384-MrcW6ZMFYlzcLA8Nl+NtUVF0sA7MsXsP1UyJoMp4YLEuNSfAP+JcXn/tWtIaxVXM"
            crossorigin="anonymous"></script>

    <title>초미니홈피 - 팬명록</title>

    <link href="https://fonts.googleapis.com/css2?family=Noto+Serif+KR:wght@200;300;400;500;600;700;900&display=swap"
          rel="stylesheet">
    <style>
        * {
            font-family: 'Noto Serif KR', serif;
        }

        .mypic {
            width: 100%;
            height: 300px;

            background-image: linear-gradient(0deg, rgba(0, 0, 0, 0.5), rgba(0, 0, 0, 0.5)),
            url('https://user-images.githubusercontent.com/116949514/204220931-b4e67aa3-92b1-42ee-87b9-4a24a8c5bcee.JPG');
            background-position: center 30%;
            background-size: cover;

            color: white;

            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
        }

        .mypost {
            width: 95%;
            max-width: 500px;
            margin: 20px auto 20px auto;

            box-shadow: 0px 0px 3px 0px black;
            padding: 20px;
        }

        .mypost > button {
            margin-top: 15px;
        }

        .mycards {
            width: 95%;
            max-width: 500px;
            margin: auto;
        }

        .mycards > .card {
            margin-top: 10px;
            margin-bottom: 10px;
        }
    </style>
    <script>
        $(document).ready(function () {
            set_temp()
            show_comment()
        });

        function set_temp() {
            $.ajax({
                type: "GET",
                url: "http://spartacodingclub.shop/sparta_api/weather/seoul",
                data: {},
                success: function (response) {
                    $('#temp').text(response['temp'])
                }
            })
        }

        function save_comment() {

            let nickname = $('#nickname').val()
            let comment = $('#comment').val()

            $.ajax({
                type: 'POST',
                url: '/homework',
                data: {nickname_give: nickname, comment_give: comment},
                success: function (response) {
                    alert(response['msg'])
                    window.location.reload()
                }
            })
        }

        function show_comment() {

            $.ajax({
                type: "GET",
                url: "/homework",
                data: {},
                success: function (response) {
                    let row = response['fanbooks']

                    for (let i = 0; i < row.length; i++) {
                        let nickname = row[i]['nickname']
                        let comment = row[i]['comment']

                        let temp_html = `<div class="card">
                                    <div class="card-body">
                                        <blockquote class="blockquote mb-0">
                                            <p>${comment}</p>
                                            <footer class="blockquote-footer">${nickname}</footer>
                                        </blockquote>
                                    </div>
                                </div>`

                        $('#comment-list').append(temp_html)
                    }
                }
            });
        }
    </script>
</head>
<body>
<div class="mypic">
    <h1>AKMU 팬명록</h1>
    <p>현재기온: <span id="temp"> </span>도</p>
</div>
<div class="mypost">
    <div class="form-floating mb-3">
        <input type="text" class="form-control" id="nickname" placeholder="url">
        <label for="floatingInput">닉네임</label>
    </div>
    <div class="form-floating">
<textarea class="form-control" placeholder="Leave a comment here" id="comment"
          style="height: 100px"></textarea>
        <label for="floatingTextarea2">응원댓글</label>
    </div>
    <button onclick="save_comment()" type="button" class="btn btn-dark">응원 남기기</button>
</div>
<div class="mycards" id="comment-list">

</div>
</body>
</html>
```