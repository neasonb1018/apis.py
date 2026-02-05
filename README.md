# apis.py
weather for Atl,Ga
# ==============================
# CONFIG
# ==============================
API_KEY = "aedb24cf4e9086946144ae4a9cea0cc0"
CITY = "Atlanta"
STATE = "GA"
COUNTRY = "US"
UNITS = "imperial"  # Fahrenheit

BASE_URL = "https://api.openweathermap.org/data/2.5/weather"
ATLANTA_TZ = ZoneInfo("America/New_York")

OUTPUT_DIR = Path("weather_data")
OUTPUT_DIR.mkdir(exist_ok=True)

# ==============================
def fetch_weather():
    params = {
        "q": f"{CITY},{STATE},{COUNTRY}",
        "appid": API_KEY,
        "units": UNITS
    }

    response = requests.get(BASE_URL, params=params, timeout=10)
    response.raise_for_status()
    return response.json()

def save_weather(data):
    local_time = datetime.now(ATLANTA_TZ)
    timestamp = local_time.strftime("%Y-%m-%d_%H-%M-%S")

    filename = OUTPUT_DIR / f"atlanta_weather_{timestamp}.json"

    payload = {
        "city": "Atlanta, GA",
        "retrieved_local_time": local_time.isoformat(),
        "timezone": "America/New_York",
        "temperature_f": data["main"]["temp"],
        "humidity_pct": data["main"]["humidity"],
        "pressure_hpa": data["main"]["pressure"],
        "weather_description": data["weather"][0]["description"],
        "wind_mph": data["wind"]["speed"],
        "cloud_coverage_pct": data["clouds"]["all"]
    }

    with open(filename, "w") as f:
        json.dump(payload, f, indent=2)

    print(f"Weather data saved: {filename}")

# ==============================
def main():
    data = fetch_weather()
    save_weather(data)

if __name__ == "__main__":
    main()


