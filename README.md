# memo
メモ用


import json
from flask import Flask, request, jsonify, render_template_string

app = Flask(__name__)

# シンプルなHTMLテンプレート
HTML_TEMPLATE = """
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <title>JSON Display</title>
    <style>
        body { font-family: sans-serif; margin: 2em; }
        pre { background: #f4f4f4; padding: 1em; border: 1px solid #ddd; border-radius: 4px; overflow-x: auto; }
        h1 { color: #333; }
        p { color: #666; }
    </style>
</head>
<body>
    <h1>受け取ったJSONデータ</h1>
    <p>以下に、HTTPリクエストボディから受け取ったJSONデータが表示されます。</p>
    <pre>{{ json_data }}</pre>
</body>
</html>
"""

@app.route('/', methods=['GET', 'POST'])
def show_json():
    """
    HTTPリクエストのボディからJSONデータを受け取り、それを整形して表示する
    """
    if request.method == 'POST':
        try:
            # リクエストボディからJSONデータを取得
            json_data = request.json
            
            # JSONデータを整形して文字列に変換
            formatted_json = json.dumps(json_data, indent=4, ensure_ascii=False)
            
            # 整形したJSONをHTMLテンプレートに渡して表示
            return render_template_string(HTML_TEMPLATE, json_data=formatted_json)
        except Exception as e:
            return jsonify({"error": "無効なJSON形式です。"})
    
    # POSTメソッド以外の場合は、説明ページを表示
    return render_template_string("""
    <h1>JSONデータを送信してください</h1>
    <p>このページにPOSTリクエストでJSONデータを送ると、その内容が表示されます。</p>
    <pre>
curl -X POST -H "Content-Type: application/json" -d '{"message": "Hello, Flask!", "number": 123}' http://127.0.0.1:5000/
    </pre>
    """)

if __name__ == '__main__':
    app.run(debug=True)
