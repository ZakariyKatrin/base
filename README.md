
# импортируем библиотеки numpy, matplotlib и ipywidgets
import numpy as np
import matplotlib.pyplot as plt
from ipywidgets import interact

# задаем количество игроков, игр и ставок
n_players = 1000 # количество игроков
n_games = 100 # количество игр
bets = np.random.randint(1, 11, size=n_players) # ставки игроков (от 1 до 10)

# создаем массивы для хранения user_id, балансов и платежей пользователей
user_id = np.arange(n_players) # идентификаторы пользователей
balances = np.zeros((n_players, n_games+1)) # балансы пользователей (начальный баланс равен 0)
payments = np.zeros((n_players, n_games+1)) # платежи пользователей (начальный платеж равен 0)

# создаем массив для хранения номеров игр, на которых пользователи заплатили
games = np.zeros((n_players, n_games+1)) # номера игр (начальный номер равен 0)

# моделируем игры в блекджек
for i in range(n_games):
    # генерируем случайные карты для дилера и игроков (от 1 до 10)
    dealer = np.random.randint(1, 11)
    players = np.random.randint(1, 11, size=n_players)
    
    # определяем, кто выиграл и кто проиграл
    wins = players > dealer # выигрыши игроков (True или False)
    losses = players < dealer # проигрыши игроков (True или False)
    
    # обновляем балансы игроков
    balances[:, i+1] = balances[:, i] + bets * wins - bets * losses
    
    # добавляем платежи пользователей на случайных играх
    payers = np.random.choice(user_id, size=100) # выбираем 100 случайных пользователей для платежа
    payments[payers, i+1] = np.random.randint(10, 101, size=100) # платежи пользователей (от 10 до 100)
    balances[payers, i+1] += payments[payers, i+1]
    
    # обновляем номера игр, на которых пользователи заплатили
    games[payers, i+1] = i+1

# включаем интерактивный режим для графиков
plt.ion()

# создаем функцию для визуализации изменения балансов всех пользователей по номерам игр и сегментации по платежам
def plot_balances(pay=0):
    plt.figure(figsize=(10, 6))
    
    for user in user_id:
        plt.plot(balances[user], label=f'User {user}')
        plt.scatter(games[user], balances[user], c=payments[user], cmap='viridis', marker='o', label='Платеж')
    
    plt.xlabel('Номер игры')
    plt.ylabel('Баланс')
    plt.title('Изменение балансов всех пользователей в блекджек по номерам игр')
    
    if pay == 0:
        plt.legend()
    else:
        plt.legend([f'Платеж {pay}'])
    
    plt.show()

# создаем виджет для выбора платежа
interact(plot_balances, pay=(0, 100))
