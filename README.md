<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>LVS</title>
</head>
<body>
    <button type="button" onclick="alert('Button clicked!')">
        <img src="{{ url_for('static', filename='your_image.jpg') }}" alt="Button Image">
    </button>
</body>
</html>
