3주차 flutter study

-페이지끼리 data를 주고 받으면서 page를 갱신하는 기존 setState 위젯 대신 provider 라이브러리?를 배웠다.
- 설치해야함
- data의 CRUD를 관리하는 class를 만들고 main에서 그녀석을 구독하도록 한다.
- 함수에 notifyListeners(); 를 넣어두면 페이지들이 갱신된다.
- data를 기기에 파일로저장/db형식으로 저장/ 서버에 저장 할 수 있는데, 여기서는 첫번째를 배웠다.
- shared_preferences 를 설치해야함



'''dart
// memo_service.dart

import 'dart:convert';

import 'package:flutter/material.dart';
import 'package:provider/provider.dart';

import 'main.dart';

// Memo 데이터의 형식을 정해줍니다. 추후 isPinned, updatedAt 등의 정보도 저장할 수 있습니다.
class Memo {
  Memo({
    required this.content,
    this.isPinned = false,
    this.updateDate,
  });

  String content;
  bool isPinned;
  DateTime? updateDate;

  Map toJson() {
    return {
      'content': content,
      'isPinned': isPinned,
      'updateDate': updateDate?.toIso8601String(),
    };
  }

  factory Memo.fromJson(json) {
    return Memo(
      content: json['content'],
      isPinned: json['isPinned'] ?? false,
      updateDate: json['updateDate'] == null
          ? null
          : DateTime.parse(json['updateDate']),
    );
  }
}

// Memo 데이터는 모두 여기서 관리
class MemoService extends ChangeNotifier {
  MemoService() {
    loadMemoList();
  }
  List<Memo> memoList = [
    Memo(
      content:
          '장보기 목록: 사과, 양파, 바나나, 애플망고, 파인애플과즙팡팡, 타트체리즙, 버터, 소고기, 오리고기와배추, 양배추, 양상추, 브로콜리너마저, 아몬드',
      isPinned: false,
      updateDate: null,
    ),
    // 더미(dummy) 데이터
  ];

  createMemo({required String content}) {
    Memo memo = Memo(
      content: content,
      updateDate: DateTime.now(),
    );
    memoList.add(memo);
    notifyListeners();
    saveMemoList();
  }

  updateMemo({required String content, required int index}) {
    Memo memo = memoList[index];
    memo.content = content;
    memo.updateDate = DateTime.now();
    notifyListeners();
    saveMemoList();
  }

  deleteMemo({required int index}) {
    memoList.removeAt(index);
    notifyListeners();
    saveMemoList();
  }

  updatePinned({required int index}) {
    Memo memo = memoList[index];
    memo.isPinned = !memo.isPinned;
    memoList = [
      ...memoList.where((element) => element.isPinned),
      ...memoList.where((element) => !element.isPinned)
    ];

    notifyListeners();
    saveMemoList();
  }

  saveMemoList() {
    List memoJsonList = memoList.map((memo) => memo.toJson()).toList();
    // [{"content": "1"}, {"content": "2"}]

    String jsonString = jsonEncode(memoJsonList);
    // '[{"content": "1"}, {"content": "2"}]'

    prefs.setString('memoList', jsonString);
    //앞에서 만든 jsonString이라는 String을 'memoList'라는 이름의 key 를 만들어 넣어주겠다
  }

  loadMemoList() {
    String? jsonString = prefs.getString('memoList');
    // '[{"content": "1"}, {"content": "2"}]'

    if (jsonString == null) return; // null 이면 로드하지 않음

    List memoJsonList = jsonDecode(jsonString);
    // [{"content": "1"}, {"content": "2"}]

    memoList = memoJsonList.map((json) => Memo.fromJson(json)).toList();
  }
}








//main.dart
import 'package:flutter/cupertino.dart';
import 'package:flutter/material.dart';
import 'package:provider/provider.dart';
import 'package:shared_preferences/shared_preferences.dart';

import 'memo_service.dart';

late SharedPreferences prefs;

void main() async {
  WidgetsFlutterBinding.ensureInitialized();
  prefs = await SharedPreferences.getInstance();
  runApp(
    MultiProvider(
      providers: [
        ChangeNotifierProvider(create: (context) => MemoService()),
      ],
      child: const MyApp(),
    ),
  );
}

class MyApp extends StatelessWidget {
  const MyApp({super.key});

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      debugShowCheckedModeBanner: false,
      home: HomePage(),
    );
  }
}

// 홈 페이지
class HomePage extends StatefulWidget {
  const HomePage({super.key});

  @override
  State<HomePage> createState() => _HomePageState();
}

class _HomePageState extends State<HomePage> {
  @override
  Widget build(BuildContext context) {
    return Consumer<MemoService>(builder: (context, memoService, child) {
      List<Memo> memoList = memoService.memoList;

      return Scaffold(
        appBar: AppBar(
          title: Text("mymemo"),
        ),
        body: memoList.isEmpty
            ? Center(child: Text("메모를 작성해 주세요"))
            : ListView.builder(
                itemCount: memoList.length, // memoList 개수 만큼 보여주기
                itemBuilder: (context, index) {
                  Memo memo = memoList[index]; // index에 해당하는 memo 가져오기
                  return ListTile(
                    // 메모 고정 아이콘
                    leading: IconButton(
                      icon: Icon(memo.isPinned
                          ? CupertinoIcons.pin_fill
                          : CupertinoIcons.pin),
                      onPressed: () {
                        memoService.updatePinned(index: index);
                      },
                    ),
                    // 메모 내용 (최대 3줄까지만 보여주도록)
                    title: Text(
                      memo.content,
                      maxLines: 3,
                      overflow: TextOverflow.ellipsis,
                    ),
                    trailing: Text(memo.updateDate == null
                        ? ''
                        : memo.updateDate.toString().substring(0, 19)),
                    onTap: () async {
                      await Navigator.push(
                        context,
                        MaterialPageRoute(
                          builder: (_) => DetailPage(index: index),
                        ),
                      );
                      if (memo.content.isEmpty) {
                        memoService.deleteMemo(index: index);
                      }
                    },
                  );
                },
              ),
        floatingActionButton: FloatingActionButton(
          child: Icon(Icons.add),
          onPressed: () async {
            memoService.createMemo(content: '');
            await Navigator.push(
              context,
              MaterialPageRoute(
                builder: (_) => DetailPage(
                  index: memoList.length - 1,
                ),
              ),
            );
            if (memoList[memoList.length - 1].content.isEmpty) {
              memoService.deleteMemo(index: memoList.length - 1);
            }
          },
        ),
      );
    });
  }
}

// 메모 생성 및 수정 페이지
class DetailPage extends StatelessWidget {
  DetailPage({super.key, required this.index});

  final int index;

  TextEditingController contentController = TextEditingController();

  @override
  Widget build(BuildContext context) {
    MemoService memoService = context.read<MemoService>();
    Memo memo = memoService.memoList[index];

    contentController.text = memo.content;
    return Scaffold(
      appBar: AppBar(
        actions: [
          IconButton(
            onPressed: () {
              // 삭제 버튼 클릭시
              showDeleteDialog(context, memoService);
            },
            icon: Icon(Icons.delete),
          )
        ],
      ),
      body: Padding(
        padding: const EdgeInsets.all(16),
        child: TextField(
          controller: contentController,
          decoration: InputDecoration(
            hintText: "메모를 입력하세요",
            border: InputBorder.none,
          ),
          autofocus: true,
          maxLines: null,
          expands: true,
          keyboardType: TextInputType.multiline,
          onChanged: (value) {
            memoService.updateMemo(content: value, index: index);
            // 텍스트필드 안의 값이 변할 때
          },
        ),
      ),
    );
  }

  void showDeleteDialog(BuildContext context, MemoService memoService) {
    showDialog(
      context: context,
      builder: (context) {
        return AlertDialog(
          title: Text("정말 삭제하시겠어요?"),
          actions: [
            // 취소 버튼
            TextButton(
              onPressed: () {
                Navigator.pop(context);
              },
              child: Text("취소"),
            ),
            // 확인 버튼
            TextButton(
              onPressed: () {
                memoService.deleteMemo(index: index); // index에 해당하는 항목 삭제
                Navigator.pop(context); // 팝업 닫기
                Navigator.pop(context); // HomePage 로 가기
              },
              child: Text(
                "확인",
                style: TextStyle(color: Colors.pink),
              ),
            ),
          ],
        );
      },
    );
  }
}
```