import speech_recognition as sr
import pyttsx3
import pywhatkit
import wikipedia
import datetime
# Initialize the text-to-speech engine
engine = pyttsx3.init()

# Set the female voice
voices = engine.getProperty('voices')
for voice in voices:
    if "female" in voice.name.lower() or "zira" in voice.name.lower():  # Adjust based on your OS
        engine.setProperty('voice', voice.id)
        break

engine.setProperty("rate", 150)  # Set speaking speed
engine.setProperty("volume", 1)  # Set volume (0.0 to 1.0)

# Function to make the assistant speak
def speak(text):
    engine.say(text)
    engine.runAndWait()

# Function to listen to user input
def listen():
    recognizer = sr.Recognizer()
    with sr.Microphone() as source:
        print("\nListening...")
        recognizer.adjust_for_ambient_noise(source)  # Adjust for background noise
        try:
            audio = recognizer.listen(source, timeout=5)
            command = recognizer.recognize_google(audio).lower()
            print(f"You said: {command}")
            return command
        except sr.WaitTimeoutError:
            speak("I didn't hear anything. Please try again.")
        except sr.UnknownValueError:
            speak("Sorry, I couldn't understand that. Can you repeat?")
        except sr.RequestError:
            speak("Network error. Please check your connection.")
    return ""

# Function to process commands
def process_command(command):
    if "play" in command:
        song = command.replace("play", "").strip()
        speak(f"Playing {song} on YouTube.")
        pywhatkit.playonyt(song)

    elif "time" in command:
        now = datetime.datetime.now()
        current_time = now.strftime("%I:%M %p")
        speak(f"The current time is {current_time}.")

    elif "date" in command:
        today = datetime.datetime.now().strftime("%A, %B %d, %Y")
        speak(f"Today's date is {today}.")

    elif "who is" in command or "what is" in command:
        query = command.replace("who is", "").replace("what is", "").strip()
        speak(f"Searching for {query} on Wikipedia.")
        try:
            summary = wikipedia.summary(query, sentences=2)
            speak(summary)
        except wikipedia.exceptions.DisambiguationError:
            speak("The term is too ambiguous. Please specify.")
        except wikipedia.exceptions.PageError:
            speak("I couldn't find any information about that.")

    elif "stop" in command or "exit" in command:
        speak("Goodbye!")
        exit()

    else:
        speak("I didn't understand that. Can you repeat?")

# Main function
def main():
    speak("Hello! I'm your assistant. How can I help you?")
    while True:
        command = listen()
        if command:
            process_command(command)

# Run the assistant
if __name__ == "__main__":
    main()
