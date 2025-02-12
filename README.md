# popitka

import requests
from bs4 import BeautifulSoup
import csv
from datetime import datetime

# Функция для получения HTML-кода страницы
def get_html(url):
    response = requests.get(url)
    if response.status_code == 200:
        return response.text
    else:
        raise Exception("Ошибка при получении данных с сайта.")

# Функция для парсинга данных о погоде
def parse_weather(html):
    soup = BeautifulSoup(html, 'html.parser')
    
    weather_data = []
    
    # Находим таблицу с погодными данными
    table = soup.find('table', class_='weather-table')
    
    # Проходимся по каждой строке таблицы
    for row in table.find_all('tr'):
        columns = row.find_all('td')
        
        if len(columns) > 0:
            date = columns[0].text.strip()
            temperature = columns[1].text.strip()
            humidity = columns[2].text.strip()
            
            weather_data.append({
                'date': date,
                'temperature': temperature,
                'humidity': humidity
            })
    
    return weather_data

# Функция для сохранения данных в CSV-файл
def save_to_csv(data, filename):
    with open(filename, mode='w', newline='', encoding='utf-8') as file:
        fieldnames = ['date', 'temperature', 'humidity']
        writer = csv.DictWriter(file, fieldnames=fieldnames)
        
        writer.writeheader()
        
        for item in data:
            writer.writerow(item)

# Основная программа
if __name__ == '__main__':
    url = 'https://www.example.com/weather'
    html = get_html(url)
    weather_data = parse_weather(html)
    
    now = datetime.now().strftime('%Y-%m-%d_%H-%M-%S')
    filename = f'weather_data_{now}.csv'
    
    save_to_csv(weather_data, filename)
    
    print(f"Данные успешно сохранены в файл {filename}")
