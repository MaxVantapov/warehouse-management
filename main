import tkinter as tk
from tkinter import ttk
from tkinter import messagebox
import json
import datetime
import os

class Product:
    """
    Класс для представления продукции на складе.
    """

    def __init__(self, name, code, quantity, price):
        """
        Инициализирует объект Product.

        Args:
            name (str): Название продукции.
            code (str): Код продукции.
            quantity (int): Количество продукции на складе.
            price (float): Цена за единицу продукции.
        """
        self.name = name
        self.code = code
        self.quantity = quantity
        self.price = price

    def __str__(self):
        """
        Возвращает строковое представление объекта Product.
        """
        return f"Название: {self.name}, Код: {self.code}, Количество: {self.quantity}, Цена: {self.price}"

class InventoryApp:
    """
    Класс для управления приложением учета продукции на складе.
    """

    def __init__(self, master):
        """
        Инициализирует приложение.

        Args:
            master (tk.Tk): Главное окно приложения.
        """
        self.master = master
        master.title("Учет продукции на складе")

        # Настройка полноэкранного режима
        screen_width = master.winfo_screenwidth()
        screen_height = master.winfo_screenheight()
        master.geometry(f"{screen_width}x{screen_height}")

        # Цветовая схема (темная)
        self.bg_color = "#121212"  # Почти черный фон
        self.fg_color = "#FFFFFF"  # Белый цвет текста
        self.entry_bg_color = "#292929"  # Темно-серый цвет фона полей ввода
        self.button_bg_color = "#1E88E5"  # Синий цвет кнопок
        self.button_hover_bg_color = "#1565C0"  # Более темный синий цвет при наведении на кнопки
        self.listbox_bg_color = "#212121"  # Темно-серый цвет фона списка продукции
        self.listbox_fg_color = "#FFFFFF"  # Белый цвет текста списка продукции

        # Настройка цветов виджетов
        master.configure(bg=self.bg_color)

        # Загрузка данных из файла
        self.products = self.load_products()

        # Инициализация журнала учета списаний
        self.write_off_log = {}
        self.load_write_off_log()

        # Создание папки для отчетов, если она не существует
        self.reports_dir = os.path.join(os.path.dirname(__file__), "reports")
        os.makedirs(self.reports_dir, exist_ok=True)

        # Инициализация роли пользователя
        self.user_role = None
        self.show_login_window()

    def show_login_window(self):
        login_window = tk.Toplevel(self.master)
        login_window.title("Авторизация")
        login_window.geometry("400x200")  # Увеличение размера окна авторизации
        login_window.configure(bg=self.bg_color)
        login_window.transient(self.master)  # Установка окна авторизации поверх основного окна
        login_window.grab_set()  # Захват фокуса на окне авторизации

        login_label = tk.Label(login_window, text="Логин:", bg=self.bg_color, fg=self.fg_color, font=("Arial", 12))
        login_label.pack(pady=10)
        login_entry = tk.Entry(login_window, font=("Arial", 12))
        login_entry.pack()

        password_label = tk.Label(login_window, text="Пароль:", bg=self.bg_color, fg=self.fg_color, font=("Arial", 12))
        password_label.pack(pady=10)
        password_entry = tk.Entry(login_window, show="*", font=("Arial", 12))
        password_entry.pack()

        login_button = tk.Button(login_window, text="Войти", command=lambda: self.login(login_entry.get(), password_entry.get(), login_window),
                                 font=("Arial", 12), bg=self.button_bg_color, fg=self.fg_color, activebackground=self.button_hover_bg_color)
        login_button.pack(pady=20)

    def login(self, username, password, login_window):
        if username == "admin" and password == "admin123":
            self.user_role = "accountant"
            messagebox.showinfo("Авторизация", "Вы вошли как бухгалтер.")
            self.username_label_text = "Бухгалтер"
            login_window.destroy()
            self.create_widgets()
        elif username == "user" and password == "user123":
            self.user_role = "employee"
            messagebox.showinfo("Авторизация", "Вы вошли как сотрудник.")
            self.username_label_text = "Сотрудник"
            login_window.destroy()
            self.create_widgets()
        else:
            messagebox.showerror("Ошибка", "Неверный логин или пароль.")

    def create_widgets(self):
        """
        Создает и размещает виджеты на форме.
        """
        # Создание фрейма для элементов формы
        self.form_frame = tk.Frame(self.master, bg=self.bg_color, padx=20, pady=20)
        self.form_frame.pack(side="left", fill="both", expand=True)

        # Создание фрейма для списка продукции
        self.product_list_frame = tk.Frame(self.master, bg=self.bg_color, padx=20, pady=20)
        self.product_list_frame.pack(side="right", fill="both", expand=True)

        # Метка с именем пользователя
        self.username_label = tk.Label(self.form_frame, text=self.username_label_text, bg=self.bg_color, fg=self.fg_color, font=("Arial", 14))
        self.username_label.grid(row=0, column=0, columnspan=2, padx=10, pady=10)

        # Метки и поля ввода
        self.name_label = tk.Label(self.form_frame, text="Название:", bg=self.bg_color, fg=self.fg_color, font=("Arial", 12))
        self.name_label.grid(row=1, column=0, padx=10, pady=10, sticky="w")
        self.name_entry = tk.Entry(self.form_frame, font=("Arial", 12), bg=self.entry_bg_color, fg=self.fg_color)
        self.name_entry.grid(row=1, column=1, padx=10, pady=10)

        self.code_label = tk.Label(self.form_frame, text="Код:", bg=self.bg_color, fg=self.fg_color, font=("Arial", 12))
        self.code_label.grid(row=2, column=0, padx=10, pady=10, sticky="w")
        self.code_entry = tk.Entry(self.form_frame, font=("Arial", 12), bg=self.entry_bg_color, fg=self.fg_color)
        self.code_entry.grid(row=2, column=1, padx=10, pady=10)

        self.quantity_label = tk.Label(self.form_frame, text="Количество:", bg=self.bg_color, fg=self.fg_color, font=("Arial", 12))
        self.quantity_label.grid(row=3, column=0, padx=10, pady=10, sticky="w")
        self.quantity_entry = tk.Entry(self.form_frame, font=("Arial", 12), bg=self.entry_bg_color, fg=self.fg_color)
        self.quantity_entry.grid(row=3, column=1, padx=10, pady=10)

        self.price_label = tk.Label(self.form_frame, text="Цена:", bg=self.bg_color, fg=self.fg_color, font=("Arial", 12))
        self.price_label.grid(row=4, column=0, padx=10, pady=10, sticky="w")
        self.price_entry = tk.Entry(self.form_frame, font=("Arial", 12), bg=self.entry_bg_color, fg=self.fg_color)
        self.price_entry.grid(row=4, column=1, padx=10, pady=10)

        # Кнопки
        if self.user_role == "accountant":
            self.add_button = tk.Button(self.form_frame, text="Добавить", command=self.add_product, font=("Arial", 12), width=15, bg=self.button_bg_color, fg=self.fg_color, activebackground=self.button_hover_bg_color)
            self.add_button.grid(row=5, column=0, columnspan=2, padx=10, pady=10)

        # Поле поиска
        self.search_label = tk.Label(self.form_frame, text="Поиск:", bg=self.bg_color, fg=self.fg_color, font=("Arial", 12))
        self.search_label.grid(row=6, column=0, padx=10, pady=10, sticky="w")
        self.search_entry = tk.Entry(self.form_frame, font=("Arial", 12), bg=self.entry_bg_color, fg=self.fg_color)
        self.search_entry.grid(row=6, column=1, padx=10, pady=10)
        self.search_entry.bind("<KeyRelease>", self.search_product)

        # Список продукции
        self.product_list = tk.Listbox(self.product_list_frame, width=40, height=10, font=("Arial", 12), bg=self.listbox_bg_color, fg=self.listbox_fg_color)
        self.product_list.pack(fill="both", expand=True)

        # Кнопки удаления и изменения
        if self.user_role == "accountant":
            self.delete_button = tk.Button(self.form_frame, text="Удалить", command=self.delete_product, font=("Arial", 12), width=10, bg=self.button_bg_color, fg=self.fg_color, activebackground=self.button_hover_bg_color)
            self.delete_button.grid(row=7, column=0, padx=10, pady=10)

            self.update_button = tk.Button(self.form_frame, text="Изменить количество", command=self.update_quantity, font=("Arial", 12), width=15, bg=self.button_bg_color, fg=self.fg_color, activebackground=self.button_hover_bg_color)
            self.update_button.grid(row=7, column=1, padx=10, pady=10)

        # Поле для ввода нового количества (спрятано по умолчанию)
        self.new_quantity_entry = tk.Entry(self.form_frame, font=("Arial", 12), bg=self.entry_bg_color, fg=self.fg_color)
        self.new_quantity_entry.grid(row=8, column=1, padx=10, pady=10)
        self.new_quantity_entry.grid_remove()

        # Кнопка подтверждения изменения количества (спрятана по умолчанию)
        self.confirm_button = tk.Button(self.form_frame, text="Подтвердить", command=self.confirm_update, font=("Arial", 12), width=10, bg=self.button_bg_color, fg=self.fg_color, activebackground=self.button_hover_bg_color)
        self.confirm_button.grid(row=8, column=0, padx=10, pady=10)
        self.confirm_button.grid_remove()

        # Кнопки для списания продукции
        self.spoilage_button = tk.Button(self.form_frame, text="Списать (порча)", command=lambda reason="порчи": self.show_write_off_dialog(reason), font=("Arial", 12), width=15, bg=self.button_bg_color, fg=self.fg_color, activebackground=self.button_hover_bg_color)
        self.spoilage_button.grid(row=9, column=0, padx=10, pady=10)

        self.processing_button = tk.Button(self.form_frame, text="Списать (обработка)", command=lambda reason="обработки": self.show_write_off_dialog(reason), font=("Arial", 12), width=15, bg=self.button_bg_color, fg=self.fg_color, activebackground=self.button_hover_bg_color)
        self.processing_button.grid(row=9, column=1, padx=10, pady=10)

        self.usage_button = tk.Button(self.form_frame, text="Списать (использование)", command=lambda reason="использования": self.show_write_off_dialog(reason), font=("Arial", 12), width=15, bg=self.button_bg_color, fg=self.fg_color, activebackground=self.button_hover_bg_color)
        self.usage_button.grid(row=10, column=0, columnspan=2, padx=10, pady=10)

        # Поле для ввода количества списания (спрятано по умолчанию)
        self.write_off_quantity_entry = tk.Entry(self.form_frame, font=("Arial", 12), bg=self.entry_bg_color, fg=self.fg_color)
        self.write_off_quantity_entry.grid(row=11, column=1, padx=10, pady=10)
        self.write_off_quantity_entry.grid_remove()

        # Кнопка подтверждения списания (спрятана по умолчанию)
        self.confirm_write_off_button = tk.Button(self.form_frame, text="Подтвердить", command=self.confirm_write_off, font=("Arial", 12), width=10, bg=self.button_bg_color, fg=self.fg_color, activebackground=self.button_hover_bg_color)
        self.confirm_write_off_button.grid(row=11, column=0, padx=10, pady=10)
        self.confirm_write_off_button.grid_remove()

        # Кнопка для отображения сводки по списаниям
        if self.user_role == "accountant":
            self.summary_button = tk.Button(self.form_frame, text="Сводка списаний", command=self.show_write_off_summary, font=("Arial", 12), width=15, bg=self.button_bg_color, fg=self.fg_color, activebackground=self.button_hover_bg_color)
        self.summary_button.grid(row=11, column=0, columnspan=2, padx=10, pady=10)

        # Кнопка для сохранения журнала в текстовый файл
        self.save_log_button = tk.Button(self.form_frame, text="Сохранить журнал (txt)", command=self.save_write_off_log_to_txt, font=("Arial", 12), width=15, bg=self.button_bg_color, fg=self.fg_color, activebackground=self.button_hover_bg_color)
        self.save_log_button.grid(row=12, column=0, columnspan=2, padx=10, pady=10)

        # Обновление списка продукции
        self.update_product_list()

    def add_product(self):
        """
        Добавляет продукцию в список.
        """
        name = self.name_entry.get()
        code = self.code_entry.get()
        quantity = self.quantity_entry.get()
        price = self.price_entry.get()

        if not all([name, code, quantity, price]):
            messagebox.showerror("Ошибка", "Заполните все поля!")
            return

        try:
            quantity = int(quantity)
            price = float(price)
        except ValueError:
            messagebox.showerror("Ошибка", "Некорректный формат данных!")
            return

        product = Product(name, code, quantity, price)
        self.products.append(product)
        self.update_product_list()
        self.clear_entry_fields()
        self.save_products()  # Сохранение данных в файл

    def update_product_list(self):
        """
        Обновляет список продукции в Listbox.
        """
        self.product_list.delete(0, tk.END)
        for product in self.products:
            self.product_list.insert(tk.END, str(product))

    def delete_product(self):
        """
        Удаляет продукцию из списка.
        """
        try:
            selection = self.product_list.curselection()[0]
            self.products.pop(selection)
            self.update_product_list()
            self.save_products()  # Сохранение данных в файл
        except IndexError:
            messagebox.showerror("Ошибка", "Выберите продукцию для удаления!")

    def update_quantity(self):
        """
        Показывает поле ввода нового количества и кнопку подтверждения.
        """
        try:
            selection = self.product_list.curselection()[0]
            self.new_quantity_entry.grid()
            self.confirm_button.grid()
            self.selected_product_index = selection
        except IndexError:
            messagebox.showerror("Ошибка", "Выберите продукцию для изменения количества!")

    def confirm_update(self):
        """
        Обновляет количество продукции.
        """
        try:
            new_quantity = int(self.new_quantity_entry.get())
            self.confirm_button.grid_remove()
        except ValueError:
            messagebox.showerror("Ошибка", "Некорректный формат данных!")

    def clear_entry_fields(self):
        """
        Очищает поля ввода.
        """
        self.name_entry.delete(0, tk.END)
        self.code_entry.delete(0, tk.END)
        self.quantity_entry.delete(0, tk.END)
        self.price_entry.delete(0, tk.END)

    def load_products(self):
        """
        Загружает данные о продукции из файла.
        """
        try:
            with open("products.json", "r") as f:
                data = json.load(f)
                return [Product(item["name"], item["code"], item["quantity"], item["price"]) for item in data]
        except FileNotFoundError:
            return []  # Возвращает пустой список, если файл не найден

    def save_products(self):
        """
        Сохраняет данные о продукции в файл.
        """
        data = [{"name": product.name, "code": product.code, "quantity": product.quantity, "price": product.price} for product in self.products]
        with open("products.json", "w") as f:
            json.dump(data, f)

    def search_product(self, event=None):
        """
        Поиск продукции в списке.
        """
        search_term = self.search_entry.get().lower()
        self.product_list.delete(0, tk.END)
        for product in self.products:
            if search_term in product.name.lower() or search_term in product.code.lower():
                self.product_list.insert(tk.END, str(product))

    def show_write_off_dialog(self, reason):
        """
        Показывает диалог списания с полем для ввода количества и кнопкой подтверждения.
        """
        try:
            selection = self.product_list.curselection()[0]
            self.write_off_quantity_entry.grid()
            self.confirm_write_off_button.grid()
            self.selected_product_index = selection
            self.write_off_reason = reason  # Сохранение причины списания
        except IndexError:
            messagebox.showerror("Ошибка", "Выберите продукцию для списания!")

    def confirm_write_off(self):
        """
        Подтверждает списание продукции.
        """
        try:
            quantity = int(self.write_off_quantity_entry.get())
            product = self.products[self.selected_product_index]
            if quantity > product.quantity:
                messagebox.showerror("Ошибка", "Недостаточно продукции на складе!")
                return

            # Списание продукции
            product.quantity -= quantity

            # Запись в журнал списаний
            self.add_write_off_to_log(product, quantity, self.write_off_reason)

            # Удаление продукции из списка, если ее количество стало равно 0
            if product.quantity == 0:
                del self.products[self.selected_product_index]

            self.update_product_list()
            self.save_products()
            self.save_write_off_log()
            self.write_off_quantity_entry.grid_remove()
            self.confirm_write_off_button.grid_remove()
        except ValueError:
            messagebox.showerror("Ошибка", "Некорректный формат данных!")

    def add_write_off_to_log(self, product, quantity, reason):
        """
        Добавляет запись о списании в журнал.
        """
        now = datetime.datetime.now()
        date_key = now.strftime("%Y-%m-%d")
        time_key = now.strftime("%H:%M:%S")

        if date_key not in self.write_off_log:
            self.write_off_log[date_key] = {}

        if time_key not in self.write_off_log[date_key]:
            self.write_off_log[date_key][time_key] = []

        self.write_off_log[date_key][time_key].append(
            {
                "product": product.name,
                "code": product.code,
                "quantity": quantity,
                "reason": reason,
                "price": product.price,
                "total_amount": quantity * product.price,
            }
        )

    def load_write_off_log(self):
        """
        Загружает журнал списаний из файла.
        """
        try:
            with open("write_off_log.json", "r") as f:
                self.write_off_log = json.load(f)
        except FileNotFoundError:
            pass  # Если файл не найден, журнал остается пустым

    def save_write_off_log(self):
        """
        Сохраняет журнал списаний в файл.
        """
        with open("write_off_log.json", "w") as f:
            json.dump(self.write_off_log, f)

    def calculate_write_off_total(self, period):
        """
        Подсчитывает сумму списаний за заданный период.

        Args:
            period (str): Период, за который нужно подсчитать сумму.
                Допустимые значения: "day", "month", "quarter".

        Returns:
            float: Сумма списаний за указанный период.
        """
        now = datetime.datetime.now()
        total = 0
        for date_key, date_data in self.write_off_log.items():
            date = datetime.datetime.strptime(date_key, "%Y-%m-%d")
            if period == "day" and date.date() == now.date():
                total += self.calculate_write_off_total_for_date(date_key)
            elif period == "month" and date.month == now.month and date.year == now.year:
                total += self.calculate_write_off_total_for_date(date_key)
            elif period == "quarter" and date.month in range(now.month - 3, now.month + 1) and date.year == now.year:
                total += self.calculate_write_off_total_for_date(date_key)
        return total

    def calculate_write_off_total_for_date(self, date_key):
        """
        Подсчитывает сумму списаний за указанную дату.
        """
        total = 0
        for time_key, write_off_entries in self.write_off_log[date_key].items():
            for entry in write_off_entries:
                total += entry["total_amount"]
        return total

    def show_write_off_summary(self):
        """
        Отображает сводку по списаниям.
        """
        day_total = self.calculate_write_off_total("day")
        month_total = self.calculate_write_off_total("month")
        quarter_total = self.calculate_write_off_total("quarter")
        messagebox.showinfo(
            "Сводка по списаниям",
            f"Сумма списаний за сегодня: {day_total:.2f}\n"
            f"Сумма списаний за этот месяц: {month_total:.2f}\n"
            f"Сумма списаний за этот квартал: {quarter_total:.2f}\n"
        )

    def save_write_off_log_to_txt(self):
        """
        Сохраняет журнал списаний в текстовый файл.
        """
        now = datetime.datetime.now()
        filename = f"write_off_log_{now.strftime('%Y-%m-%d_%H-%M-%S')}.txt"
        filepath = os.path.join(self.reports_dir, filename)

        with open(filepath, "w", encoding="utf-8") as f:
            for date_key, date_data in self.write_off_log.items():
                f.write(f"\nДата: {date_key}\n")
                for time_key, write_off_entries in date_data.items():
                    for entry in write_off_entries:
                        f.write(
                            f"Время: {time_key} | Продукция: {entry['product']} | "
                            f"Код: {entry['code']} | Количество: {entry['quantity']} | "
                            f"Причина: {entry['reason']} | Цена: {entry['price']} | "
                            f"Сумма: {entry['total_amount']:.2f}\n"
                        )

                # Подсчет сумм за день, месяц и квартал
                day_total = self.calculate_write_off_total_for_date(date_key)
                month_total = self.calculate_write_off_total("month")
                quarter_total = self.calculate_write_off_total("quarter")
                f.write(
                    f"\nСумма списаний за {date_key}: {day_total:.2f}\n"
                    f"Сумма списаний за этот месяц: {month_total:.2f}\n"
                    f"Сумма списаний за этот квартал: {quarter_total:.2f}\n"
                )

# Создание главного окна приложения
root = tk.Tk()

# Создание объекта InventoryApp
app = InventoryApp(root)

# Запуск цикла обработки событий
root.mainloop()
