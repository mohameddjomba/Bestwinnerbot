import telebot
from telebot import types

TOKEN = '7561839376:AAFzMptESqnXuB7C8Y4QdLv6kqwhQ-VkleI'
bot = telebot.TeleBot(TOKEN)

# Dictionnaire pour stocker les étapes utilisateurs
user_steps = {}

# Commande /start
@bot.message_handler(commands=['start'])
def send_welcome(message):
    markup = types.ReplyKeyboardMarkup(resize_keyboard=True)
    item1 = types.KeyboardButton("1. Avoir mon code promo")
    item2 = types.KeyboardButton("2. Récupérer son cadeau")
    item3 = types.KeyboardButton("3. Avoir des informations")
    markup.add(item1, item2, item3)
    bot.send_message(message.chat.id, "Bienvenue sur Best Winner Guide ! Choisissez une option :", reply_markup=markup)

# Gestion des messages
@bot.message_handler(func=lambda message: True)
def handle_message(message):
    if message.text.startswith("1"):
        bot.send_message(message.chat.id, "Ton code promo est : MDC24")
    elif message.text.startswith("2"):
        bot.send_message(message.chat.id, "As-tu utilisé mon code promo MDC24 ? (Oui / Non)")
        user_steps[message.chat.id] = "ask_code"
    elif message.text.startswith("3"):
        info = ("Bienvenue chez Best Winner Guide !\n"
                "Ici, nous te proposons les meilleurs pronostics pour maximiser tes chances de succès dans les paris sportifs.\n"
                "Rejoins notre canal officiel : https://t.me/bestwinner224\n"
                "Utilise mon code promo MDC24 pour avoir 200% de bonus et 50% de remboursement sur tes pertes.")
        bot.send_message(message.chat.id, info)
    else:
        step = user_steps.get(message.chat.id)
        if step == "ask_code":
            if message.text.lower() == "oui":
                bot.send_message(message.chat.id, "Envoie-moi une capture du numéro de ton compte.")
                user_steps[message.chat.id] = "ask_account"
            elif message.text.lower() == "non":
                bot.send_message(message.chat.id, "Utilise mon code promo MDC24 avant de continuer.")
            else:
                bot.send_message(message.chat.id, "Réponds par Oui ou Non.")
        elif step == "ask_account":
            bot.send_message(message.chat.id, "Merci ! Maintenant, envoie-moi la capture du lien partagé.")
            user_steps[message.chat.id] = "ask_link"
        elif step == "ask_link":
            bot.send_message(message.chat.id, "Merci beaucoup ! Maintenant contacte @djomba2024 pour récupérer ton cadeau !")
            save_user(message)

# Fonction pour sauvegarder les informations
def save_user(message):
    with open('candidats.txt', 'a', encoding='utf-8') as f:
        f.write(f"Utilisateur {message.from_user.username} ({message.from_user.id}) a terminé toutes les étapes.\n")

# Lancer le bot
bot.polling()
