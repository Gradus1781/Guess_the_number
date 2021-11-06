# Guess_the_number
# A mini-game - "Guess the number" with simple rules and settings
from random import randint
from math import log, ceil


def answer_to_the_question() -> bool:
    while True:
        answer = input(">>> ").lower()
        if answer == "да":
            return True
        elif answer == "нет":
            return False
        else:
            continue


def settings():
    # Здесь пользователь может задать правую границу диапазона угадываемого числа, а также включить/выключить подсказки.
    while True:
        print("\033[0mПрименить стандартные настройки игры?(да, нет)")
        if answer_to_the_question():
            print("\033[0mПо умолчанию будут заданы стандартные настройки игры")
            start = 1
            end = 100
            prompt_more = "\033[31mСлишком много, попробуйте еще раз"
            prompt_less = "\033[34mСлишком мало, попробуйте еще раз"
            return start, end, prompt_more, prompt_less
        else:
            start = 1
            while True:
                end = input("Задайте правую границу числа: ")
                if end.isdigit():
                    end = int(end)
                    if end <= 1:
                        print(f"Правая граница числа не может быть меньше или равной 1")
                        continue
                    else:
                        print(f"Задана правая граница числа - {end}")
                        break
                else:
                    print("Возможно вы ввели не число.")
                    continue
            print("Необходимы ли подсказки?(да, нет) (Совет: рекомендуется оставить эту опцию включенной)")
            if answer_to_the_question():
                prompt_more = "\033[31mСлишком много, попробуйте еще раз"
                prompt_less = "\033[34mСлишком мало, попробуйте еще раз"
            else:
                prompt_more = prompt_less = "Неверно! Попробуйте ещё раз"
                print("Надеюсь, ваша интуиция вас не подведёт!")
            return start, end, prompt_more, prompt_less


def correct_input(left, right):
    # Проверка правильности ввода числа пользователем.
    while True:
        num = input("Предполагаемый ответ: ")
        if num.isdigit():
            num = int(num)
            if left <= num <= right:
                return num
            else:
                print("Выход за границы чисел. Повторите ввод.")
                continue
        else:
            print("Возможно вы ввели не число.")
            continue


def main():
    while True:
        START, END, PROMPT_MORE, PROMPT_LESS = settings()
        HIDDEN_NUMBER = randint(START, END)
        number_of_attempts = 0
        # Если пользователь выбрал игру без подсказок, то игра не предложит ему сдаться и не выведет статистику
        # среднего кол-ва попыток на заданный диапазон числа.
        if PROMPT_MORE == "Неверно! Попробуйте ещё раз":
            average_number_of_attempts = [ceil(log(END, 2)), float("inf")]
        else:
            average_number_of_attempts = [ceil(log(END, 2))] * 2
        while True:
            print(f"\033[0mВведите число (оно должно быть в пределах от {START} до {END} включительно): ")
            choice = correct_input(START, END)
            number_of_attempts += 1
            if choice == HIDDEN_NUMBER:
                print(f"Да, это число {HIDDEN_NUMBER}!")
                print()
                print(f'\033[32mСтатистика: ')
                print(f'Затрачено попыток: {number_of_attempts}')
                break
            elif choice > HIDDEN_NUMBER:
                print(PROMPT_MORE)
            elif choice < HIDDEN_NUMBER:
                print(PROMPT_LESS)
            else:
                print("\033[31mВозможно вы ввели не число.")
                continue
            # Если пользователь затрачивает больше попыток, чем в среднем можно затратить на угадывание числа,
            # игра предложит пользователю сдаться.
            if number_of_attempts > average_number_of_attempts[1]:
                print("\033[0mСлишком сложно? Не хотите ли завершить игру и посмотреть ответ?(да,нет)")
                if answer_to_the_question():
                    print(f"Вы выбрали сдаться. Загаданное число: {HIDDEN_NUMBER}")
                    break
                else:
                    average_number_of_attempts[1] += 5
        if average_number_of_attempts[1] != float("inf"):
            print(
                f'В среднем, чтобы угадать число в диапазоне от {START} до {END}, затрачивается {average_number_of_attempts[0]} попыток')
        print('\033[0mХотите сыграть ещё раз?(да, нет)')
        if answer_to_the_question():
            continue
        else:
            print("\033[0mСпасибо за игру! Возвращайтесь к нам ещё!")
            break


print('Добро пожаловать в игру: "Угадай число"')
print("""Правила игры просты: 
Я загадываю число, ты пытаешься угадать. 
Если число угадано, игра завершается.
Есть возможность настроить игру на свой лад.""")
print()
main()
