import requests
from flask import Flask, request, render_template_string

 Flask(__name__)

API_KEY = "your_openweathermap_api_key"  # replace with your own API key from openweathermap.org

HTML_TEMPLATE = """
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Weather App</title>
    <style>
        body { font-family: Arial, sans-serif; margin: 30px; text-align: center; }
        input { padding: 10px; width: 250px; }
        button { padding: 10px 20px; }
        .card { margin-top: 20px; padding: 20px; border: 1px solid #ccc; display: inline-block; }
    </style>
</head>
<body>
    <h1>Weather App</h1>
    <form method="post">
        <input type="text" name="city" placeholder="Enter city name" required>
        <button type="submit">Get Weather</button>
    </form>
    {% if weather %}
        <div class="card">
            <h2>{{ weather.city }}</h2>
            <p><strong>Temperature:</strong> {{ weather.temp }} °C</p>
            <p><strong>Condition:</strong> {{ weather.description }}</p>
            <p><strong>Humidity:</strong> {{ weather.humidity }}%</p>
            <p><strong>Wind Speed:</strong> {{ weather.wind }} m/s</p>
        </div>
    {% endif %}
</body>
</htmlS>
"""

@app.route("/", methods=["GET", "POST"])
def home():
    weather = None
    if request.method == "POST":
        city = request.form.get("city")
        url = f"http://api.openweathermap.org/data/2.5/weather?q={city}&appid={API_KEY}&units=metric"
        try:
            response = requests.get(url, timeout=5)
            data = response.json()
            if data.get("cod") == 200:
                weather = {
                    "city": data["name"],
                    "temp": data["main"]["temp"],
                    "description": data["weather"][0]["description"].title(),
                    "humidity": data["main"]["humidity"],
                    "wind": data["wind"]["speed"]
                }
            else:
                weather = {"city": "Not Found", "temp": "-", "description": "-", "humidity": "-", "wind": "-"}
        except Exception:
            weather = {"city": "Error", "temp": "-", "description": "-", "humidity": "-", "wind": "-"}
    return render_template_string(HTML_TEMPLATE, weather=weather)

if __name__ == "__main__":
    app.run(debug=True)
