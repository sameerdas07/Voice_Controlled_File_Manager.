# Voice_Controlled_File_Manager.
The Voice-Controlled File Manager is a Python-based desktop application that allows users to manage files and folders using voice commands. The system recognizes spoken instructions and performs file operations such as creating, opening, deleting, renaming, and searching files or directories.

import speech_recognition as sr
import pyttsx3
import os
import shutil
import tkinter as tk
from tkinter import messagebox, scrolledtext

# Text-to-Speech setup
engine = pyttsx3.init()
def speak(text):
    engine.say(text)
    engine.runAndWait()

# Voice recognition setup
r = sr.Recognizer()

# Function to listen voice command
def listen_command():
    with sr.Microphone() as source:
        speak("Listening for your command...")
        text_area.insert(tk.END, "Listening...\n")
        audio = r.listen(source)
        try:
            command = r.recognize_google(audio).lower()
            text_area.insert(tk.END, f"You said: {command}\n")
            return command
        except:
            speak("Sorry, I did not catch that.")
            text_area.insert(tk.END, "Could not understand voice.\n")
            return ""

# File manager actions
def file_manager(command):
    try:
        if "open" in command:
            folder = command.replace("open", "").strip()
            path = os.path.join(os.path.expanduser("~"), folder)
            if os.path.exists(path):
                os.startfile(path)
                speak(f"Opening {folder}")
                text_area.insert(tk.END, f"Opened {folder}\n")
            else:
                speak("Folder not found.")
                text_area.insert(tk.END, "Folder not found.\n")

        elif "delete" in command:
            file = command.replace("delete", "").strip()
            if os.path.exists(file):
                os.remove(file)
                speak("File deleted.")
                text_area.insert(tk.END, f"Deleted {file}\n")
            else:
                speak("File not found.")
                text_area.insert(tk.END, "File not found.\n")

        elif "create folder" in command:
            folder = command.replace("create folder", "").strip()
            os.makedirs(folder, exist_ok=True)
            speak(f"Folder {folder} created.")
            text_area.insert(tk.END, f"Created folder {folder}\n")

        elif "move" in command:
            parts = command.replace("move", "").strip().split("to")
            if len(parts) == 2:
                src = parts[0].strip()
                dest = parts[1].strip()
                shutil.move(src, dest)
                speak(f"Moved {src} to {dest}")
                text_area.insert(tk.END, f"Moved {src} to {dest}\n")
            else:
                speak("Invalid move command format.")
                text_area.insert(tk.END, "Invalid move command.\n")

        elif "exit" in command or "quit" in command:
            speak("Goodbye Sameer!")
            window.destroy()

        else:
            speak("Command not recognized.")
            text_area.insert(tk.END, "Command not recognized.\n")

    except Exception as e:
        text_area.insert(tk.END, f"Error: {str(e)}\n")

# Function triggered by button
def voice_action():
    command = listen_command()
    if command:
        file_manager(command)

# GUI Setup
window = tk.Tk()
window.title("Voice Controlled File Manager")
window.geometry("600x400")
window.configure(bg="#222831")

title = tk.Label(window, text="🎤 Voice Controlled File Manager", 
                 font=("Arial", 16, "bold"), bg="#222831", fg="#FFD369")
title.pack(pady=10)

btn = tk.Button(window, text="🎙 Speak Command", command=voice_action, 
                font=("Arial", 14), bg="#FFD369", fg="#222831", relief="raised")
btn.pack(pady=10)

text_area = scrolledtext.ScrolledText(window, width=70, height=15, bg="#393E46", fg="white", font=("Consolas", 10))
text_area.pack(pady=10)

exit_btn = tk.Button(window, text="❌ Exit", command=window.destroy, 
                     font=("Arial", 12), bg="#FF4B4B", fg="white")
exit_btn.pack(pady=5)

window.mainloop()
