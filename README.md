# telegram-compteur-bot
Bot Telegram pour comptabiliser les enquêtes faites et à faire
from telegram import Update
from telegram.ext import ApplicationBuilder, CommandHandler, ContextTypes
import json, os

TOKEN = "8881502456:AAHSYXxafmutOFQqMURFM0pcBEY3WOLB1ic"

DATA_FILE = "data.json"

if not os.path.exists(DATA_FILE):
    with open(DATA_FILE, "w") as f:
        json.dump({}, f)

def load_data():
    with open(DATA_FILE) as f:
        return json.load(f)

def save_data(data):
    with open(DATA_FILE, "w") as f:
        json.dump(data, f)

CODES = {
    "medecine": "01",
    "egs": "02",
    "droit": "03",
    "lettres": "04",
    "sciences": "05",
    "ens": "06",
    "essa": "07",
    "espa": "08"
}

ANNEES = {
    "L1": "01", "L2": "02", "L3": "03",
    "M1": "04", "M2": "05",
    "INT7": "07", "INT8": "08",
    "THESE": "09", "DOC": "06"
}

async def add(update: Update, context: ContextTypes.DEFAULT_TYPE):
    data = load_data()
    fac = context.args[0].lower()
    an = context.args[1].upper()

    key = f"{fac}_{an}"
    data[key] = data.get(key, 0) + 1
    save_data(data)

    numero = f"{CODES[fac]}_{ANNEES[an]}_{data[key]:03d}"
    await update.message.reply_text(f"Numéro attribué : {numero}")

app = ApplicationBuilder().token(TOKEN).build()
app.add_handler(CommandHandler("add", add))
app.run_polling()
