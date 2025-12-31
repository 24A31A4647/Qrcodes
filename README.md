from flask import Flask, request, render_template_string
import qrcode
import base64
from io import BytesIO

app = Flask(__name__)

HTML = """
<!DOCTYPE html>
<html>
<head>
    <title>QR Code Generator</title>
    <style>
        body {
            font-family: Arial;
            background: #f2f2f2;
        }
        .box {
            width: 350px;
            margin: 100px auto;
            background: white;
            padding: 25px;
            text-align: center;
            border-radius: 10px;
            box-shadow: 0 5px 15px rgba(0,0,0,0.2);
        }
        input, button {
            width: 95%;
            padding: 10px;
            margin: 10px 0;
        }
        button {
            background: #0072ff;
            color: white;
            border: none;
            font-size: 16px;
            cursor: pointer;
        }
        img {
            margin-top: 20px;
        }
    </style>
</head>
<body>

<div class="box">
    <h2>QR Code Generator</h2>

    <form method="post">
        <input type="text" name="data" placeholder="Enter text or URL" required>
        <button type="submit">Generate QR</button>
    </form>

    {% if qr %}
        <img src="data:image/png;base64,{{ qr }}" width="200">
    {% endif %}
</div>

</body>
</html>
"""

@app.route("/", methods=["GET", "POST"])
def home():
    qr_img = None

    if request.method == "POST":
        data = request.form["data"]

        qr = qrcode.make(data)
        buffer = BytesIO()
        qr.save(buffer, format="PNG")

        qr_img = base64.b64encode(buffer.getvalue()).decode()

    return render_template_string(HTML, qr=qr_img)

if __name__ == "__main__":
    app.run(debug=True)

