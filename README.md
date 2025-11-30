# Telegram to Google Calendar Bot with AI

An n8n workflow that automatically creates Google Calendar events from Telegram messages using Google Gemini AI for intelligent event extraction.

## ✨ Features

- 📱 **Text message parsing** - Send plain text messages to create calendar events
- 🖼️ **Image analysis** - Upload event posters/flyers and extract event details
- 🌍 **Automatic timezone handling** - Configured for Europe/Madrid (easily customizable)
- 📍 **Location extraction** - Automatically detects and adds event locations
- 🤖 **AI-powered parsing** - Uses Google Gemini to intelligently extract:
  - Event title
  - Date and time
  - Duration
  - Location
  - Description
- ✅ **Smart confirmation** - Sends beautifully formatted confirmation messages
- ⚠️ **Error handling** - Graceful error messages when something goes wrong

## 🎯 Use Cases

**Example 1 - Simple text:**
```
Meeting with team tomorrow at 2pm for 1 hour
```
✅ Creates event: "Meeting with team" on tomorrow at 14:00-15:00

**Example 2 - With location:**
```
Dentist appointment on December 15th at 10:30am at Carrer de Balmes 243
```
✅ Creates event with location

**Example 3 - Photo:**
Upload an event poster with caption "Add this to my calendar"
✅ AI analyzes the image and extracts event details

## 📋 Prerequisites

You'll need the following accounts and credentials:

1. **Telegram Bot**
   - Message [@BotFather](https://t.me/botfather) on Telegram
   - Create a new bot with `/newbot`
   - Save the API token

2. **Google Calendar**
   - A Google account with Calendar access
   - You'll set up OAuth2 within n8n

3. **Google Gemini API**
   - Go to [Google AI Studio](https://aistudio.google.com/apikey)
   - Create an API key
   - Free tier available

4. **n8n Instance**
   - Self-hosted or cloud n8n
   - Version 1.0+ recommended

## 🚀 Setup Instructions

### Step 1: Import the Workflow

1. Download `telegram-calendar-workflow-sanitized.json`
2. In n8n, go to **Workflows** → **Import from File**
3. Select the downloaded JSON file
4. The workflow will appear in your n8n instance

### Step 2: Configure Credentials

#### Telegram Bot
1. In n8n, go to **Credentials** → **Add Credential**
2. Search for "Telegram API"
3. Enter your bot token from BotFather
4. Save as "Telegram Bot"

#### Google Calendar
1. Go to **Credentials** → **Add Credential**
2. Search for "Google Calendar OAuth2 API"
3. Click "Connect my account"
4. Follow the OAuth flow to authorize n8n
5. Save as "Google Calendar OAuth2"

#### Google Gemini
1. Go to **Credentials** → **Add Credential**
2. Search for "Google PaLM API" or "Google Gemini API"
3. Enter your Gemini API key
4. Save as "Google Gemini API"

### Step 3: Update Workflow Credentials

1. Open the imported workflow
2. Click on each node with a credential icon
3. Select the appropriate credential you just created
4. Save the workflow

**Nodes that need credentials:**
- Telegram Trigger → Telegram Bot
- Download Photo → Telegram Bot
- Message a model → Google Gemini API
- Analyze an image → Google Gemini API
- Create an event → Google Calendar OAuth2
- Send Confirmation → Telegram Bot
- All "Send Error Message" nodes → Telegram Bot

### Step 4: Activate the Workflow

1. Toggle the workflow to **Active**
2. The Telegram Trigger will start listening for messages

### Step 5: Test It!

1. Open Telegram and message your bot
2. Send: `Team meeting tomorrow at 3pm`
3. Check your Google Calendar for the new event!

## ⚙️ Customization

### Change Timezone

The workflow is set to **Europe/Madrid**. To change:

1. Open workflow settings (gear icon)
2. Set **Timezone** to your timezone
3. Update the Gemini prompts in:
   - "Message a model" node
   - "Analyze an image" node
   
   Replace `Europe/Madrid` with your timezone (e.g., `America/New_York`)

### Adjust Confirmation Message

Edit the **"Send Confirmation"** node to customize the message format.

Current format:
```
✅ Event created successfully!

📅 *Event Title*

🕐 Monday, December 1, 2025
⏰ 14:00 - 15:00

📍 Location (if present)

Description...
```

### Add Voice Message Support

The workflow has a placeholder for voice messages. To enable:

1. Add a **Speech-to-Text** node after "Download Voice"
2. Options:
   - **OpenAI Whisper** (recommended)
   - **Google Speech-to-Text**
3. Connect to the Parse JSON Response node

## 🛠️ Workflow Structure

```
Telegram Trigger
    ↓
Check Message Type (Switch)
    ├─→ Text → Process Text → Gemini Analysis
    ├─→ Photo → Download → Process → Gemini Vision
    └─→ Voice → (Not yet implemented)
        ↓
Parse JSON Response
    ↓
Create Google Calendar Event
    ↓
Send Confirmation (or Error)
```

## 🐛 Troubleshooting

### "Credential not found" error
- Make sure you've created all three credentials
- Verify each node is using the correct credential

### Events created in wrong timezone
- Check workflow settings → Timezone
- Update Gemini prompts to use your timezone
- Ensure Google Calendar account timezone matches

### Image analysis not working
- Verify the "Process Photo" node has "Include Other Input Fields" enabled
- Check that Gemini API key is valid
- Ensure you're using a Gemini model with vision support (gemini-2.5-flash or gemini-1.5-flash)

### No response from bot
- Check that workflow is **Active**
- Verify Telegram webhook is configured
- Test the Telegram Trigger node manually

## 📝 Example Messages

### Simple events:
- `Lunch tomorrow at noon`
- `Call with John on Friday at 3pm`
- `Dentist appointment Dec 15 at 10:30am`

### With duration:
- `Team meeting tomorrow 2-4pm`
- `Workshop next Monday from 9am to 5pm`

### With location:
- `Meeting at Starbucks Barcelona tomorrow at 3pm`
- `Dinner at La Taverna on Saturday 8pm`
- `Conference at Gran Via 123, Madrid on Dec 10`

### All-day events:
- `Vacation from Jan 5 to Jan 10`
- `Conference all day tomorrow`
