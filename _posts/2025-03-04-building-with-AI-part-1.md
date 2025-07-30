---
title: Building with AI
date: 2025-03-03 12:00:00 +0200
categories: [AI, LLM]
tags: [AI, LLM, development, learning, project]
pin: False
---
# Telegram bot

## Overview

**Key use cases:**

When learning a new language it is sometimes easier to listen instead of reading.

This bot can be used to transform text into audio for this purpose.

## Prompts

## Code

```
import os
from telegram import Update
from telegram.ext import Application, CommandHandler, MessageHandler, ContextTypes, filters
from gtts import gTTS
import tempfile

# Replace 'YOUR_BOT_TOKEN' with the token you got from BotFather
TOKEN = 'yup-do-not-share-this'

async def start(update: Update, context: ContextTypes.DEFAULT_TYPE):
    """Send a message when the command /start is issued."""
    welcome_message = (
        "👋 Welcome to the Text-to-Speech Bot!\n\n"
        "Simply send me any text message, and I'll convert it to audio for you.\n"
        "You can also use /help to see available commands."
    )
    await update.message.reply_text(welcome_message)

async def help_command(update: Update, context: ContextTypes.DEFAULT_TYPE):
    """Send a message when the command /help is issued."""
    help_message = (
        "🔊 Text-to-Speech Bot Commands:\n\n"
        "- Just send any text message to convert it to audio\n"
        "- /start - Start the bot\n"
        "- /help - Show this help message"
    )
    await update.message.reply_text(help_message)

async def text_to_speech(update: Update, context: ContextTypes.DEFAULT_TYPE):
    """Convert text message to speech and send audio."""
    try:
        # Send "processing" message
        processing_message = await update.message.reply_text("🎵 Converting your text to speech...")
  
        # Get the text from the message
        text = update.message.text
  
        # Create a temporary file
        with tempfile.NamedTemporaryFile(delete=False, suffix='.mp3') as tmp_file:
            # Convert text to speech
            tts = gTTS(text=text, lang='en')
            tts.save(tmp_file.name)
      
            # Send the audio file
            with open(tmp_file.name, 'rb') as audio:
                await update.message.reply_voice(voice=audio)
      
            # Delete the temporary file
            os.unlink(tmp_file.name)
  
        # Delete the processing message
        await processing_message.delete()
  
    except Exception as e:
        error_message = f"Sorry, an error occurred: {str(e)}"
        await update.message.reply_text(error_message)

def main():
    """Start the bot."""
    # Create the Application
    application = Application.builder().token(TOKEN).build()

    # Add handlers
    application.add_handler(CommandHandler("start", start))
    application.add_handler(CommandHandler("help", help_command))
    application.add_handler(MessageHandler(filters.TEXT & ~filters.COMMAND, text_to_speech))

    # Start the bot
    print("Bot is starting...")
    application.run_polling()

if __name__ == '__main__':
    main()
```

# Output
