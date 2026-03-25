# Итоговое задание по курсу «Основы синтаксиса языка программирования Python».
# Виселица - это вротой проект в рамках курса
# Функционал игры:
считывает слово из текстого файла, где ранее был сформирован список слов для игры, выбирая одно случайное слово из списка
считываем и выводим кол-во букв в загаданном слове
определяем есть ли вводимая пользователем, если да - отображается, там, ге она имеется в слове, если нет - выводит, что нет такого
Если слово угадали - поздравляют и предлагают сыграть ещё, если не угадали, то выводит слово, которое было и предлагает сыграть новую партию
# Код игры:

iimport random
import os

def load_words(filename: str) -> list: #загружаем слово из текстового файла
    
    if not os.path.exists(filename):
        print(f"Ошибка: файл '{filename}' не найден.")
        return []
    try:
        with open(filename, 'r', encoding='utf-8') as file:
            words = [line.strip().lower() for line in file if line.strip()]
        if not words:
            print("Ошибка: файл пуст или содержит только пустые строки.")
            return []
        return words
    except Exception as e:
        print(f"Ошибка при чтении файла: {e}")
        return []

def display_word(secret_word: str, guessed_letters: set) -> str:
    """Показывает слово с угаданными буквами и подчёркиваниями для остальных."""
    return ' '.join(
        letter if letter in guessed_letters else '_'
        for letter in secret_word
    )

def play_hangman(words: list, max_attempts: int = 10):   #Осовной цикл игры  #увеличены попытки о 10
    # Выбор случайного слова происходит КАЖДЫЙ РАЗ при запуске функции
    secret_word = random.choice(words)
    guessed_letters = set()
    wrong_letters = set()

    print(f"\nДобро пожаловать в игру 'Виселица'!")
    print(f"Слово содержит {len(secret_word)} букв. У вас {max_attempts} попыток.")

    while len(wrong_letters) < max_attempts and '_' in display_word(secret_word, guessed_letters):
        print("\n" + display_word(secret_word, guessed_letters))
        print(f"Неверные буквы: {', '.join(sorted(wrong_letters)) if wrong_letters else 'нет'}")
        print(f"Осталось попыток: {max_attempts - len(wrong_letters)}")

        guess = input("Введите букву: ").strip().lower()

        # Валидация ввода
        if len(guess) != 1 or not guess.isalpha():
            print("Пожалуйста, введите ровно одну букву алфавита.")
            continue
        if guess in guessed_letters or guess in wrong_letters:
            print("Вы уже вводили эту букву. Попробуйте другую.")
            continue

        # Проверка буквы
        if guess in secret_word:
            guessed_letters.add(guess)
            print(f"Отлично! Буква '{guess}' есть в слове.")
        else:
            wrong_letters.add(guess)
            print(f"Увы, буквы '{guess}' нет в слове.")

    # Проверка исхода игры
    if '_' not in display_word(secret_word, guessed_letters):
        print(f"\nПоздравляем! Вы угадали слово: '{secret_word}'!")
    else:
        print(f"\nИгра окончена! Вы проиграли.")
        print(f"Загаданное слово было: '{secret_word}'.")

def main():
    words = load_words('words.txt')
    if not words:
        print("Невозможно начать игру: нет слов для угадывания.")
        return

    while True:
        play_hangman(words)  # КАЖДЫЙ РАЗ выбирается новое случайное слово
        play_again = input("\nХотите сыграть ещё раз? (да/нет): ").strip().lower()
        if play_again not in ('да', 'д', 'yes', 'y'):
            print("Спасибо за игру! До новых встреч!")
            break

if __name__ == "__main__":
    main()

# Пример вывода:
# Добро пожаловать в игру 'Виселица'!    
# Слово содержит 6 букв. У вас 6 попыток.
# _ _ _ _ _ _
# Неверные буквы: нет
# Осталось попыток: 6
# Введите букву: 