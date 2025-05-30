from aiogram import Bot, Dispatcher, types
from aiogram.utils import executor
import logging

TOKEN = "YOUR_TOKEN_HERE"  # Удалим потом, чтобы был секрет

logging.basicConfig(level=logging.INFO)
bot = Bot(token=TOKEN)
dp = Dispatcher(bot)

tables = {}

@dp.message_handler(commands=['start'])
async def start(message: types.Message):
    await message.answer(
        "Привет! Я кальян-бот.\n\n"
        "/add <номер стола> — добавить кальян.\n"
        "/list — показать заказы.\n"
        "/reset — сбросить все заказы."
    )

@dp.message_handler(commands=['add'])
async def add_hookah(message: types.Message):
    parts = message.text.strip().split()
    if len(parts) != 2:
        await message.answer("⚠️ Используй: /add <номер стола>\nПример: /add 3")
        return
    table_number = int(parts[1])
    tables[table_number] = tables.get(table_number, 0) + 1
    await message.answer(f"✅ Стол {table_number}: {tables[table_number]} кальяна(ов).")

@dp.message_handler(commands=['list'])
async def list_tables(message: types.Message):
    if not tables:
        await message.answer("Нет активных заказов.")
        return
    text = "📋 Список заказов:\n" + "\n".join(f"Стол {t}: {c} кальяна(ов)" for t, c in tables.items())
    await message.answer(text)

@dp.message_handler(commands=['reset'])
async def reset_orders(message: types.Message):
    tables.clear()
    await message.answer("♻️ Все заказы сброшены.")

if __name__ == "__main__":
    executor.start_polling(dp, skip_updates=True)
