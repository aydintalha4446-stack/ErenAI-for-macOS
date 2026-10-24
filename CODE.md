import os
import time
import re
import math   
import platform
import psutil
import ollama  # İNTERNETSİZ ÇALIŞAN YEREL YAPAY ZEKÂ KÜTÜPHANESİ
from datetime import datetime

class MultilingualLocalAI:
    def __init__(self):
        self.name = "Eren"
        self.user = "Developer"
        self.model_name = "gemma2:2b" # Yerel model adın
        self.current_lang = "tr"  # Varsayılan dil: Türkçe (tr, en, de, es desteklenir)
        
        # Çok dilli sistem talimatları ve çeviri sözlüğü
        self.prompts = {
            "tr": "Senin adın Eren. MacBook Pro üzerinde çalışan, siber güvenlik uzmanı bir yapay zekasın. Kısa, havalı ve zekice Türkçe cevap ver.",
            "en": "Your name is Eren. You are a cybersecurity expert AI running on MacBook Pro. Give short, cool, and smart answers in English.",
            "de": "Dein Name ist Eren. Du bist eine KI für Cybersicherheit auf dem MacBook Pro. Antworte kurz, cool und intelligent auf Deutsch.",
            "es": "Tu nombre es Eren. Eres una IA experta en ciberseguridad en el MacBook Pro. Da respuestas cortas, geniales e inteligentes en español."
        }

    def set_language(self, text):
        """Kullanıcının dil tercihini metne göre otomatik algılar veya komutla değiştirir."""
        t = text.lower()
        if "english" in t or "ingilizce" in t or "/en" in t:
            self.current_lang = "en"
            return "Language switched to English. 🌐"
        elif "deutsch" in t or "almanca" in t or "/de" in t:
            self.current_lang = "de"
            return "Sprache auf Deutsch gewechselt. 🌐"
        elif "español" in t or "ispanyolca" in t or "/es" in t:
            self.current_lang = "es"
            return "Idioma cambiado a español. 🌐"
        elif "türkçe" in t or "turkish" in t or "/tr" in t:
            self.current_lang = "tr"
            return "Dil Türkçe olarak ayarlandı. 🌐"
        return None

    def local_ai_chat(self, user_message):
        """İnternet olmasa bile MacBook'un işlemcisini kullanarak çok dilli cevap üretir."""
        try:
            system_prompt = self.prompts.get(self.current_lang, self.prompts["tr"])
            response = ollama.chat(model=self.model_name, messages=[
                {'role': 'system', 'content': system_prompt},
                {'role': 'user', 'content': user_message}
            ])
            return response['message']['content']
        except Exception as e:
            return f"[{self.name}]: Yerel AI motoruna bağlanamadım. Terminalde 'ollama run {self.model_name}' komutunun açık olduğundan emin ol."
                
    def system_status(self):
        ram = psutil.virtual_memory().percent
        cpu = psutil.cpu_percent()
        return f"M1 Pro CPU: %{cpu} | RAM: %{ram} | Security: Blackhole Mode Active (Lang: {self.current_lang.upper()})"
            
    def main_loop(self):
        print("\n" + "="*70)
        print("   EREN OTONOM YEREL AI CORE - MULTILINGUAL & OFFLINE MODE ")
        print("="*70)
        print(f"[{self.name}]: Desteklenen diller / Supported languages: Türkçe (tr), English (en), Deutsch (de), Español (es)")
        print(f"[{self.name} LOG]: {self.system_status()}")
        print(f"[{self.name}]: Sistem hazır. Dil değiştirmek için 'English', 'Deutsch' veya 'Español' yazabilirsin. (Çıkış için 'kapat' / 'exit')\n")

        while True:
            userInput = input(f"[{self.user}]: ")

            if userInput.strip() == "":
                continue

            if userInput.lower() in ["kapat", "çık", "güle güle", "exit", "quit"]:
                print(f"[{self.name}]: Güvenlik protokolü kapatılıyor. Görüşürüz!")
                break
              
            # Dil değiştirme kontrolü
            lang_change_msg = self.set_language(userInput)
            if lang_change_msg:
                print(f"[{self.name}]: {lang_change_msg}\n")
                continue

            # Sistem Durumu Komutu
            if "sistem" in userInput.lower() or "status" in userInput.lower() or "durum" in userInput.lower():
                print(f"[{self.name}]: {self.system_status()}\n")
                continue
    
            print(f"[{self.name} Düşünüyor / Processing...]")
        
            # Çok dilli yapay zekâdan cevabı alıyoruz
            answer = self.local_ai_chat(userInput)
            print(f"[{self.name}]: {answer}\n")

if __name__ == "__main__":
    ai_core = MultilingualLocalAI()
    ai_core.main_loop()
