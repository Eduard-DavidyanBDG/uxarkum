# uxarkum
import telebot
from pyowm import OWM
from pyowm.utils.config import get_default_config
from telebot import types


class Manager:
    def __init__(self):
        self.bot = telebot.TeleBot(token="1892952559:AAFIyjUT1h-IPoM6jZJH2yT7ev29YApaSyE")
        self.sendbot = self.bot
        self.ForWorking()

    def ForWorking(self):
        return self.sendbot


M = Manager()


class ForSending:

    def __init__(self, message):
        self.message = message
        self.text = message.text
        self.chat_id = message.chat.id
    @M.bot.message_handler
    def func(self):
        return self.chat_id, self.text

C = ForSending()


class OwmManager:
    def __init__(self):
        self.config_dict = get_default_config()
        self.config_dict['language'] = 'ru'
        self.owm = OWM("1742160613:AAEpQ2cK-Sw-57QuMttololjalQdgm-bWJU", self.config_dict)
        self.controller()

    def controller(self):
        return self.owm


O = OwmManager()


class StarterWeather(ForSending):
    def __init__(self, message):
        super().__init__(message)
        self.startofweather()

    @M.bot.message_handler(commands=["get_weather", "weather"])
    def startofweather(self):
        return M.sendbot.send_message(self.chat_id, "Which City do you want??")


class StarterInfo(ForSending):
    def __init__(self, message):
        super().__init__(message)
        self.markup_inline = types.InlineKeyboardMarkup()
        self.item_yes = types.InlineKeyboardButton(text="Ayo", callback_data="yes")
        self.item_no = types.InlineKeyboardButton(text="Che", callback_data="no")
        self.button()

    @M.bot.message_handler(commands=["get_info", "info"])
    def button(self):
        self.markup_inline.add(self.item_yes, self.item_no)
        return M.sendbot.send_message(self.chat_id, "Uzumes imanas qo masin???🤔", reply_markup=self.markup_inline)


class Answer:
    def __init__(self, call):
        self.call = call.data
        self.do_answer()

    @M.bot.callback_query_handler(func=lambda call: True)
    def do_answer(self):
        if self.call == "yes":
            self.answer = M.sendbot.send_message(self.call.message.chat.id, "Mi ban gri")

        elif self.call.data == "no":
            self.answer = M.sendbot.send_message(self.call.message.chat.id,
                                                 "Lav de ete ches uzum 😢, de es gnaci davay")
        return self.answer


class IdAndName(ForSending):
    def __init__(self, message):
        super().__init__(message)
        self.do_Name()
        self.do_id()

    @M.bot.message_handler(commands=["get_info", "Name"])
    def do_Name(self):
        self.answer2 = M.sendbot.send_message(self.chat_id,
                                              f"Your Name: {self.message.from_user.first_name} {self.message.from_user.last_name}")
        return self.answer2

    @M.bot.message_handler(commands=["get_info", "ID"])
    def do_id(self):
        self.answer1 = M.sendbot.send_message(self.chat_id, f"Your ID: {self.message.from_user.id}")
        return self.answer1


class Doaction(ForSending):
    def __init__(self, message):
        super().__init__(message)
        self.mgr = O.owm.weather_manager()
        self.observation = self.mgr.weather_at_place(self.text)
        self.w = self.observation.weather
        self.action()

    @M.bot.message_handler(content_types="text")
    def action(self):
        self.w1 = self.w.temperature('celsius')["temp"]
        self.w = self.observation.weather.detailed_status
        self.do = M.sendbot.send_message(self.chat_id, f"Сейчас {self.w} \nTemp: {self.w1}°")
        return self.do


M.bot.polling(none_stop=True, interval=0)
