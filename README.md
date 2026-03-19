import json
import os

DATA_FILE = 'library.json'

def load_books():
    if os.path.exists(DATA_FILE):
        with open(DATA_FILE, 'r', encoding='utf-8') as f:
            return json.load(f)
    return []

def save_books(books):
    with open(DATA_FILE, 'w', encoding='utf-8') as f:
        json.dump(books, f, ensure_ascii=False, indent=2)

def add_book(books):
    print("\n--- Добавление книги ---")
    book = {
        'title': input("Название: "),
        'author': input("Автор: "),
        'genre': input("Жанр: "),
        'year': input("Год издания: "),
        'description': input("Описание: "),
        'is_read': False,
        'is_favorite': False
    }
    books.append(book)
    save_books(books)
    print("Книга добавлена!\n")

def view_books(books):
    if not books:
        print("Библиотека пуста.\n")
        return


    sort_by = input("Сортировать по (title/author/year/none): ").lower()
    if sort_by in ['title', 'author', 'year']:
        books = sorted(books, key=lambda x: x[sort_by])


    genre_filter = input("Фильтр по жанру (или Enter для всех): ")
    if genre_filter:
        books = [b for b in books if b['genre'].lower() == genre_filter.lower()]


    status_filter = input("Фильтр по статусу (read/unread/all): ").lower()
    if status_filter == 'read':
        books = [b for b in books if b['is_read']]
    elif status_filter == 'unread':
        books = [b for b in books if not b['is_read']]


    print("\n--- Список книг ---")
    for i, book in enumerate(books, 1):
        status = "✓ Прочитана" if book['is_read'] else "○ Не прочитана"
        favorite = "★ Избранное" if book['is_favorite'] else ""
        print(f"{i}. {book['title']} - {book['author']} ({book['year']})")
        print(f"   Жанр: {book['genre']} | {status} {favorite}")
        print(f"   Описание: {book['description']}\n")

def search_books(books):
    query = input("Поиск (по названию, автору или описанию): ").lower()
    results = [
        book for book in books
        if query in book['title'].lower() or
           query in book['author'].lower() or
           query in book['description'].lower()
    ]

    if results:
        print("\n--- Результаты поиска ---")
        for book in results:
            print(f"{book['title']} - {book['author']}")
    else:
        print("Ничего не найдено.\n")

def toggle_read_status(books):
    try:
        index = int(input("Номер книги для изменения статуса: ")) - 1
        if 0 <= index < len(books):
            books[index]['is_read'] = not books[index]['is_read']
            status = "прочитана" if books[index]['is_read'] else "не прочитана"
            print(f"Статус изменён: {status}\n")
            save_books(books)
        else:
            print("Неверный номер.\n")
    except ValueError:
        print("Введите число.\n")

def toggle_favorite(books):
    try:
        index = int(input("Номер книги для добавления/удаления из избранного: ")) - 1
        if 0 <= index < len(books):
            books[index]['is_favorite'] = not books[index]['is_favorite']
            action = "добавлена в" if books[index]['is_favorite'] else "удалена из"
            print(f"Книга {action} избранного.\n")
            save_books(books)
        else:
            print("Неверный номер.\n")
    except ValueError:
        print("Введите число.\n")

def show_favorites(books):
    favorites = [book for book in books if book['is_favorite']]
    if favorites:
        print("\n--- Избранное ---")
        for book in favorites:
            print(f"{book['title']} - {book['author']}")
    else:
        print("В избранном нет книг.\n")

def delete_book(books):
    try:
        index = int(input("Номер книги для удаления: ")) - 1
        if 0 <= index < len(books):
            removed = books.pop(index)
            print(f"Удалена: {removed['title']}\n")
            save_books(books)
        else:
            print("Неверный номер.\n")
    except ValueError:
        print("Введите число.\n")

def main():
    books = load_books()
    print("Добро пожаловать в T‑Библиотеку!")

    while True:
        print("\n--- Меню ---")
        print("1. Добавить книгу")
        print("2. Просмотреть книги")
        print("3. Поиск книги")
        print("4. Изменить статус прочитанности")
        print("5. Добавить/удалить из избранного")
        print("6. Показать избранное")
        print("7. Удалить книгу")
        print("0. Выход")

        choice = input("Выберите действие: ")

        if choice == '1':
            add_book(books)
        elif choice == '2':
            view_books(books)
        elif choice == '3':
            search_books(books)
        elif choice == '4':
            toggle_read_status(books)
        elif choice == '5':
            toggle_favorite(books)
        elif choice == '6':
            show_favorites(books)
        elif choice == '7':
            delete_book(books)
        elif choice == '0':
            print("До свидания!")
            break
        else:
            print("Неверный выбор. Попробуйте снова.\n")

if __name__ == '__main__':
    main()
