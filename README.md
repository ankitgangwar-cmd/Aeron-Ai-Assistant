# Aeron-Ai-Assistant
Aeron is a Python-based voice assistant that can recognize voice commands, search Wikipedia, open websites, play music, tell the time, launch VS Code, and send emails.

import pyttsx3
import speech_recognition as sr
import datetime
import wikipedia
import webbrowser
import os
import smtplib


engine = pyttsx3.init('sapi5')
voices = engine.getProperty('voices')
engine.setProperty('voice', voices[0].id)


def speak(audio):
    engine.say(audio)
    engine.runAndWait()


def wishMe():
    hour = int(datetime.datetime.now().hour)
    if 0 <= hour < 12:
        speak("Good morning!")
    elif 12 <= hour < 18:
        speak("Good afternoon!")
    else:
        speak("Good evening!")

    speak("I am Aeron boss. Please tell me how I may help you.")
def takecommand():
    # It takes microphone input from the user and returns a string output.
    r = sr.Recognizer()
    r.energy_threshold = 300
    r.dynamic_energy_threshold = True

    mic_list = sr.Microphone.list_microphone_names()
    device_index = 1
    for index, name in enumerate(mic_list):
        if 'microphone' in name.lower():
            device_index = index
            break

    with sr.Microphone(device_index=device_index) as source:
        print("Adjusting for ambient noise...")
        r.adjust_for_ambient_noise(source, duration=1)
        print("Listening...")
        audio = r.listen(source, timeout=10, phrase_time_limit=5)

    try:
        print("Recognizing...")
        query = r.recognize_google(audio, language='en-in')
        print(f"User said: {query}\n")
        return query.lower()
    except sr.UnknownValueError:
        print("Voice not understood. Say that again please...")
        return "None"
    except sr.RequestError:
        print("Network issue while recognizing speech. Check your internet connection.")
        return "None"
    except Exception:
        print("Say that again please...")
        return "None"
def sendEmail(to, content):
    server = smtplib.SMTP('smtp.gmail.com', 587)
    server.ehlo()
    server.starttls()
    server.login('your_email@gmail.com', 'your_password')
    server.sendmail('your_email@gmail.com', to, content)
    server.close()



if __name__ == "__main__":
    wishMe()
    while True:
        query = takecommand()

        if query == "None":
            continue

        # Logic for executing tasks based on query
        if 'wikipedia' in query:
            speak('Searching Wikipedia...')
            query = query.replace("wikipedia", "")
            results = wikipedia.summary(query, sentences=2)
            speak("According to Wikipedia")
            print(results)
            speak(results)

        elif 'open youtube' in query:
            webbrowser.open("youtube.com")

        elif 'open google' in query:
            webbrowser.open("google.com")

        elif 'open stackoverflow' in query:
            webbrowser.open("stackoverflow.com")

        elif 'play music' in query:
            music_dir = 'D:\\Non Critical\\songs\\Favorite Songs2'
            songs = os.listdir(music_dir)
            print(songs)
            os.startfile(os.path.join(music_dir, songs[0]))

        elif 'the time' in query:
            strTime = datetime.datetime.now().strftime("%H:%M:%S")
            speak(f"Sir, the time is {strTime}")

        elif 'open code' in query:
            codePath = "C:\\Users\\User\\AppData\\Local\\Programs\\Microsoft VS Code\\Code.exe"
            os.startfile(codePath)

        elif 'email to ankitgangwar' in query:
            try:
                speak("What should I say?")
                content = takecommand()
                to = "ankitgangwaryourEmail@gmail.com"
                sendEmail(to, content)
            except Exception as e:
                print(e)
                speak("Sorry my friend ankit bhai, I am not able to send this email.")
