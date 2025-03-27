# Speech-recognition
import speech_recognition as sr
import pyttsx3
import requests
import datetime


engine = pyttsx3.init()



def speak(text):
    engine.say(text)
    engine.runAndWait()



def listen():
    recognizer = sr.Recognizer()


    with sr.Microphone() as source:
        print("Listening...")
        recognizer.adjust_for_ambient_noise(source)
        audio = recognizer.listen(source)

    try:
        print("Recognizing...")
        query = recognizer.recognize_google(audio)
        print("You said:", query)
        return query.lower()
    except sr.UnknownValueError:
        print("Sorry, I could not understand the audio.")
        return None
    except sr.RequestError:
        print("Sorry, there was an issue with the speech recognition service.")
        return None



def get_weather(city):
    api_key = "YOUR_API_KEY"
    url = f"http://api.openweathermap.org/data/2.5/weather?q={city}&appid={api_key}&units=metric"

    response = requests.get(url)
    data = response.json()

    if data["cod"] == 200:
        main = data["main"]
        temperature = main["temp"]
        humidity = main["humidity"]
        description = data["weather"][0]["description"]

        weather_report = f"The temperature in {city} is {temperature}°C with {description}. The humidity is {humidity}%."
        return weather_report
    else:
        return "City not found or invalid API key."



def get_time():
    now = datetime.datetime.now()
    return now.strftime("%I:%M %p")



def execute_query(query):
    if "weather" in query:
        city = query.replace("weather in", "").strip()
        weather_report = get_weather(city)
        speak(weather_report)
    elif "time" in query:
        current_time = get_time()
        speak(f"The current time is {current_time}.")
    elif "hello" in query or "hi" in query:
        speak("Hello! How can I help you today?")
    elif "exit" in query or "quit" in query:
        speak("Goodbye!")
        exit()



def print_hi(name):
    print(f'Hi, {name}')


# Main function
def main():
    print_hi('Prachi')
    speak("Hello! Welcome")

    while True:
        query = listen()
        if query:
            execute_query(query)


if __name__ == "__main__":

    main();
