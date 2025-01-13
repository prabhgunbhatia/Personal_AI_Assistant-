---

# Personal AI Assistant

A voice-activated personal AI assistant that can chat with OpenAI's GPT-3, control applications, set reminders, send emails, and more. This assistant is built using Python and leverages libraries like `speech_recognition`, `win32com.client`, `openai`, `schedule`, and `smtplib` to provide various functionalities.

## Features

### 1. **Speech Recognition**
   The assistant listens to user commands through the microphone and converts speech into text using Google's Web Speech API.

   ```python
   def take_command():
       r = sr.Recognizer()
       with sr.Microphone() as source:
           r.pause_threshold = 1
           try:
               audio = r.listen(source)
               print("Recognizing...")
               query = r.recognize_google(audio, language="en-us")
               print(f"User said: {query}")
               return query
           except sr.UnknownValueError:
               speak("Sorry, I could not understand. Please repeat.")
               return ""
           except Exception as e:
               speak(f"An error occurred: {str(e)}")
               return ""
   ```

### 2. **OpenAI GPT-3 Chat**
   The assistant communicates with OpenAI's GPT-3 to generate responses to user queries. The conversation is saved, enabling the assistant to remember past interactions.

   ```python
   def get_openai_response(prompt):
       try:
           response = openai.Completion.create(
               model="text-davinci-003",
               prompt=prompt,
               temperature=1,
               max_tokens=256,
               top_p=1,
               frequency_penalty=0,
               presence_penalty=0
           )
           return response["choices"][0]["text"].strip()
       except Exception as e:
           return f"Error: {str(e)}"
   ```

### 3. **Text-to-Speech (TTS) Feedback**
   The assistant provides voice feedback using the Windows SAPI (Speech API), allowing hands-free interaction.

   ```python
   def speak(text):
       speaker.Speak(text)
   ```

### 4. **Open Applications**
   The assistant can launch applications like Notepad or Google Chrome based on user commands.

   ```python
   def open_application(app_name):
       try:
           if "notepad" in app_name.lower():
               subprocess.Popen("notepad.exe")
           elif "chrome" in app_name.lower():
               subprocess.Popen("C:\\Program Files\\Google\\Chrome\\Application\\chrome.exe")
           else:
               speak("Sorry, I don't know how to open that application.")
       except Exception as e:
           speak(f"Error opening application: {str(e)}")
   ```

### 5. **Reminder System**
   The assistant can set daily reminders that notify you at the specified time. You can set reminders like "remind me to call mom at 18:00."

   ```python
   def schedule_reminder(time_input, reminder_text):
       schedule.every().day.at(time_input).do(lambda: speak(f"Reminder: {reminder_text}"))
       speak(f"Reminder set for {time_input}")
   ```

### 6. **Sending Emails**
   The assistant can send emails via SMTP (Gmail) based on user input. You can dictate the recipient, subject, and body of the email.

   ```python
   def send_email(recipient, subject, body):
       sender_email = "your-email@example.com"
       password = "your-password"
       msg = MIMEMultipart()
       msg['From'] = sender_email
       msg['To'] = recipient
       msg['Subject'] = subject
       msg.attach(MIMEText(body, 'plain'))
       try:
           server = smtplib.SMTP('smtp.gmail.com', 587)
           server.starttls()
           server.login(sender_email, password)
           server.send_message(msg)
           server.quit()
           speak("Email sent successfully!")
       except Exception as e:
           speak(f"Error sending email: {str(e)}")
   ```

### 7. **Saving Conversation History**
   All conversations with GPT-3 are saved in a text file for later review.

   ```python
   def save_conversation(prompt, response):
       if not os.path.exists("Openai"):
           os.mkdir("Openai")
       filename = f"Openai/{datetime.now().strftime('%Y%m%d_%H%M%S')}.txt"
       with open(filename, "w") as f:
           f.write(f"Prompt: {prompt}\nResponse: {response}")
   ```

### 8. **Multi-threading for Scheduled Tasks**
   The assistant uses the `schedule` library to manage tasks like reminders, running them in a separate thread.

   ```python
   def run_pending_tasks():
       while True:
           schedule.run_pending()
           time.sleep(1)

   # Start a separate thread to run scheduled tasks
   import threading
   task_thread = threading.Thread(target=run_pending_tasks)
   task_thread.daemon = True
   task_thread.start()
   ```

## Installation

To run this AI assistant, follow these steps:

### 1. **Install Dependencies**
   Ensure you have the required libraries installed. You can install them via `pip`:

   ```bash
   pip install speechrecognition pyttsx3 openai schedule smtplib
   ```

### 2. **Configure OpenAI API**
   You'll need an API key from OpenAI. Set up a `config.py` file containing the following:

   ```python
   apikey = "your-openai-api-key-here"
   ```

### 3. **Set Up Gmail SMTP (Optional)**
   If you want to use the email functionality, enable SMTP access for your Gmail account. For enhanced security, use an app-specific password or enable less secure apps.

### 4. **Running the Assistant**
   Once you've installed the dependencies and configured everything, you can start the assistant by running the script:

   ```bash
   python ai_assistant.py
   ```

   The assistant will greet you and start listening for commands.

## Usage

Once the assistant is running, you can interact with it using voice commands such as:

- **"What's the weather today?"**
- **"Open Notepad"**
- **"Remind me to call mom at 18:00"**
- **"Send email to John Doe"**
- **"Reset chat history"**
- **"Thank you"** (to exit)

### Example Workflow:
1. **Start the Assistant**: It greets you and waits for a command.
2. **Ask a Question**: For example, "What's the weather like today?"
3. **Get a Response**: The assistant chats with GPT-3 and speaks the answer.
4. **Set a Reminder**: You can say, "Remind me to call mom at 18:00."
5. **Send an Email**: Say "Send email to John Doe," and the assistant will guide you through composing and sending the email.

## Troubleshooting

- **Microphone Issues**: Ensure that your microphone is working properly and is accessible by the Python script.
- **OpenAI API Key**: Make sure you have a valid API key in `config.py`.
- **Email Sending**: If you can't send emails, verify that you have enabled SMTP for Gmail and are using the correct credentials.

---
