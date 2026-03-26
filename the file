

!pip install google-generativeai

import os
os.environ["GOOGLE_API_KEY"] = "no no no"

import os
import google.generativeai as genai
from google.generativeai.types import HarmCategory, HarmBlockThreshold

# Set up the API
genai.configure(api_key=os.environ["GOOGLE_API_KEY"])

# System prompt - tells Gemini how to act as a coach
system_prompt = """You are an expert martial arts coach and mentor with decades of experience in boxing, Muay Thai, kickboxing, wrestling, and MMA.

Your job is to:
- Give personalized advice on techniques, form, and strategy
- Help diagnose and treat training injuries safely
- Provide mental game and mindset coaching
- Create training plans tailored to the fighter
- Answer questions about nutrition, recovery, and conditioning

Always be encouraging, realistic and motivating"""

# Initialize the model
# Changed model_name from 'gemini-1.5-flash' to 'gemini-pro'

# List available models and find one that supports generateContent
available_models = genai.list_models()
selected_model_name = None
for m in available_models:
    if "generateContent" in m.supported_generation_methods:
        selected_model_name = m.name
        break

if selected_model_name:
    print(f"Using model: {selected_model_name}")
    model = genai.GenerativeModel(
        model_name=selected_model_name,
        system_instruction=system_prompt,
        safety_settings={
            HarmCategory.HARM_CATEGORY_DANGEROUS_CONTENT: HarmBlockThreshold.BLOCK_NONE,
            HarmCategory.HARM_CATEGORY_HARASSMENT: HarmBlockThreshold.BLOCK_NONE,
        }
    )
else:
    raise ValueError("No model supporting 'generateContent' found. Please check your API key and permissions.")

# Keep track of conversation history
conversation_history = []

def ask_coach(user_message):
    """Send a message to your AI coach and get a response"""

    # Add user message to history
    conversation_history.append({
        "role": "user",
        "parts": [user_message]
    })

    # Send to Gemini
    response = model.generate_content(
        contents=conversation_history,
        stream=False
    )

    # Get the response
    assistant_message = response.text

    # Add assistant response to history (so it remembers)
    conversation_history.append({
        "role": "model",
        "parts": [assistant_message]
    })

    return assistant_message
# INTERACTIVE CHAT MODE
print("🥊 Welcome to your AI Martial Arts Coach!")
print("Ask me anything about fighting, training, injuries, technique...")
print("Type 'exit' to quit\n")

while True:
    user_input = input("You: ").strip()

    if user_input.lower() == "exit":
        print("Coach: Great training session! Keep grinding! 💪")
        break

    if not user_input:
        print("(Please ask something)\n")
        continue

    print("Coach: ", end="", flush=True)
    response = ask_coach(user_input)
    print(response)
    print("\n" + "-" * 80 + "\n")

for question in questions:
    print(f"You: {question}")
    response = ask_coach(question)
    print(f"Coach: {response}\n")
    print("-" * 80 + "\n")

import google.generativeai as genai
from google.generativeai.types import HarmCategory, HarmBlockThreshold
import os
import json
from datetime import datetime
import time

# Set up API
genai.configure(api_key=os.environ.get("GOOGLE_API_KEY"))

# System prompt for the coach
system_prompt = """You are an expert martial arts coach and mentor with decades of experience in boxing, Muay Thai, kickboxing, wrestling, and MMA.

Your job is to:
- Give personalized advice on techniques, form, and strategy
- Help diagnose and treat training injuries safely
- Provide mental game and mindset coaching
- Create training plans tailored to the fighter
- Answer questions about nutrition, recovery, and conditioning

Always be encouraging, realistic, and safety-conscious. Keep responses concise but detailed (2-4 paragraphs). If something requires a doctor, tell them to see one.

IMPORTANT: The user may reference their training history from previous sessions. Use this context to give better, more personalized advice."""

# Initialize the model

available_models = genai.list_models()
selected_model_name = None
for m in available_models:
    if "generateContent" in m.supported_generation_methods:
        selected_model_name = m.name
        break


# Training history file
HISTORY_FILE = "training_history.json"

def load_history():
    """Load training history from file"""
    if os.path.exists(HISTORY_FILE):
        try:
            with open(HISTORY_FILE, 'r') as f:
                return json.load(f)
        except:
            return []
    return []

def save_history(history):
    """Save training history to file"""
    with open(HISTORY_FILE, 'w') as f:
        json.dump(history, f, indent=2)

def get_history_summary(history):
    """Create a summary of recent training sessions for context"""
    if not history:
        return "No previous training history."

    # Get last 5 sessions
    recent_sessions = history[-5:]
    summary = "Recent training history:\n"

    for i, session in enumerate(recent_sessions, 1):
        date = session.get('date', 'Unknown date')
        topics = session.get('topics', [])
        summary += f"\n{i}. Session on {date}\n"
        if topics:
            summary += f"   Topics discussed: {', '.join(topics[:3])}\n"

    return summary

def extract_topics(messages):
    """Extract main topics from conversation"""
    topics = set()
    martial_arts = ['boxing', 'muay thai', 'kickboxing', 'wrestling', 'mma', 'jiu jitsu', 'karate', 'taekwondo']
    training_aspects = ['footwork', 'technique', 'injury', 'conditioning', 'mindset', 'nutrition', 'recovery',
                       'strength', 'cardio', 'speed', 'power', 'defense', 'stance', 'combinations']

    text = ' '.join([msg.get('content', '').lower() for msg in messages])

    for art in martial_arts:
        if art in text:
            topics.add(art.title())

    for aspect in training_aspects:
        if aspect in text:
            topics.add(aspect.title())

    return list(topics)[:5]  # Return top 5 topics

def main():
    """Main chat loop with history saving"""

    # Load previous history
    training_history = load_history()
    conversation_history = []

    print("🥊 Welcome to your AI Martial Arts Coach!")
    print("=" * 60)

    # Show history summary
    if training_history:
        print("\n📋 Your Training History:")
        print(get_history_summary(training_history))
    else:
        print("\n📋 This is your first session! Let's build your training foundation.")

    print("\n" + "=" * 60)
    print("Ask me anything about fighting, training, injuries, technique...")
    print("Type 'exit' to quit | 'history' to see past sessions")
    print("=" * 60 + "\n")

    while True:
        try:
            user_input = input("You: ").strip()

            if user_input.lower() == "exit":
                # Save session before exiting
                if conversation_history:
                    topics = extract_topics(conversation_history)
                    session_data = {
                        'date': datetime.now().strftime("%Y-%m-%d %H:%M"),
                        'messages_count': len(conversation_history),
                        'topics': topics
                    }
                    training_history.append(session_data)
                    save_history(training_history)
                    print(f"\n✅ Session saved! ({len(conversation_history)} messages)")

                print("Coach: Great training session! Keep grinding! 💪")
                break

            if user_input.lower() == "history":
                if training_history:
                    print("\n📚 Full Training History:")
                    for i, session in enumerate(training_history, 1):
                        date = session.get('date', 'Unknown')
                        count = session.get('messages_count', '?')
                        topics = ', '.join(session.get('topics', []))
                        print(f"\n{i}. {date}")
                        print(f"   Messages: {count}")
                        print(f"   Topics: {topics}")
                    print()
                else:
                    print("No training history yet. Start chatting!\n")
                continue

            if not user_input:
                print("(Please ask something)\n")
                continue

            # Add user message to conversation
            conversation_history.append({
                "role": "user",
                "parts": [user_input]
            })

            # Show typing indicator
            print("Coach: ", end="", flush=True)

            # Send to Gemini
            response = model.generate_content(
                contents=conversation_history,
                stream=False
            )

            coach_response = response.text

            # Add coach response to conversation
            conversation_history.append({
                "role": "model",
                "parts": [coach_response]
            })

            print(coach_response)
            print("\n" + "-" * 60 + "\n")

        except KeyboardInterrupt:
            # Save on Ctrl+C
            if conversation_history:
                topics = extract_topics(conversation_history)
                session_data = {
                    'date': datetime.now().strftime("%Y-%m-%d %H:%M"),
                    'messages_count': len(conversation_history),
                    'topics': topics
                }
                training_history.append(session_data)
                save_history(training_history)
                print(f"\n\n✅ Session saved! ({len(conversation_history)} messages)")
            print("\nCoach: Keep training! 💪")
            break

        except Exception as e:
            print(f"\n❌ Error: {str(e)}")
            print("(Make sure your API key is set: export GOOGLE_API_KEY='your_key_here')\n")
            continue

if __name__ == "__main__":
    main()
