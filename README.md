# Project-
Ai driven personal assistant 
Ai driven personal assistant import speech_recognition as sr import pyttsx3 import datetime import wikipedia import webbrowser import os import random import smtplib # For sending emails import ssl # For secure email connection from email.mime.text import MIMEText from email.mime.multipart import MIMEMultipart

--- 1. Text-to-Speech (TTS) Engine ---
def speak(audio): engine = pyttsx3.init('sapi5') # SAPI5 is a Microsoft speech API, works on Windows voices = engine.getProperty('voices') # You can change voice by index: 0 for male, 1 for female (depends on installed voices) engine.setProperty('voice', voices[0].id) engine.say(audio) engine.runAndWait()

--- 2. Speech-to-Text (STT) Engine ---
def take_command(): r = sr.Recognizer() with sr.Microphone() as source: print("Listening...") r.pause_threshold = 1 # seconds of non-speaking audio before a phrase is considered complete audio = r.listen(source)

try:
    print("Recognizing...")
    query = r.recognize_google(audio, language='en-in') # Using Google's speech recognition
    print(f"User said: {query}\n")

except Exception as e:
    print("Say that again please...")
    return "None"
return query.lower()
--- 3. Core Assistant Functions ---
def wish_me(): hour = int(datetime.datetime.now().hour) if 0 <= hour < 12: speak("Good Morning!") elif 12 <= hour < 18: speak("Good Afternoon!") else: speak("Good Evening!") speak("I am your personal assistant. How can I help you today?")

def send_email(to_email, subject, body): sender_email = "YOUR_EMAIL@example.com" # Replace with your email sender_password = "YOUR_EMAIL_PASSWORD" # Replace with your email password or App Password

message = MIMEMultipart()
message["From"] = sender_email
message["To"] = to_email
message["Subject"] = subject
message.attach(MIMEText(body, "plain"))

context = ssl.create_default_context()
try:
    with smtplib.SMTP_SSL("smtp.gmail.com", 465, context=context) as server:
        server.login(sender_email, sender_password)
        server.sendmail(sender_email, to_email, message.as_string())
    speak("Email has been sent!")
except Exception as e:
    print(f"Error sending email: {e}")
    speak("Sorry, I could not send the email.")
--- 4. Main Logic ---
if name == "main": wish_me() while True: query = take_command()

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

    elif 'open stack overflow' in query:
        webbrowser.open("stackoverflow.com")

    elif 'play music' in query:
        music_dir = 'C:\\Users\\YourUser\\Music' # Change this to your music directory
        songs = os.listdir(music_dir)
        if songs:
            os.startfile(os.path.join(music_dir, random.choice(songs)))
        else:
            speak("No music files found in the directory.")

    elif 'the time' in query:
        str_time = datetime.datetime.now().strftime("%H:%M:%S")
        speak(f"Sir, the time is {str_time}")

    elif 'open code' in query:
        code_path = "C:\\Users\\YourUser\\AppData\\Local\\Programs\\Microsoft VS Code\\Code.exe" # Change to your VS Code path
        os.startfile(code_path)

    elif 'email to' in query:
        try:
            speak("To whom should I send the email?")
            to = take_command()
            # You'd need a more robust way to map names to emails
            # For simplicity, let's assume 'to' is the actual email address for now
            if "@" not in to: # Simple check, you'd need a lookup table or prompt
                speak("Please specify a valid email address.")
                continue

            speak("What should be the subject?")
            subject = take_command()

            speak("What should I say?")
            content = take_command()

            send_email(to, subject, content)
        except Exception as e:
            print(e)
            speak("Sorry, I am not able to send this email at the moment.")

    elif 'exit' in query or 'stop' in query:
        speak("Goodbye!")
        break