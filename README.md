# Animated-pencil
# Импортируем необходимые модули
import tkinter as tk
from tkinter import colorchooser, filedialog  # Модули для выбора цвета и файлового диалога
import pygame  # Для воспроизведения музыки
from PIL import Image, ImageDraw  # Для работы с изображениями

# Определяем класс DrawingApp
class DrawingApp:
    # Конструктор класса
    def __init__(self, root):
        self.root = root  # Главное окно приложения
        self.root.title("Рисование")  # Заголовок окна

        # Создаем холст для рисования
        self.canvas = tk.Canvas(root, width=800, height=600, bg="white")
        self.canvas.pack()

        # Инициализируем переменные для отслеживания положения мыши и настроек рисования
        self.old_x = None
        self.old_y = None
        self.eraser_mode = False  # Режим ластика
        self.pen_color = "black"  # Цвет карандаша

        # Привязываем обработчики событий мыши
        self.canvas.bind("<B1-Motion>", self.handle_motion)
        self.canvas.bind("<ButtonRelease-1>", self.handle_release)
        self.canvas.bind("<Button-1>", self.handle_click)

        # Создаем кнопки управления
        self.pencil_button = tk.Button(root, text="Карандаш", command=self.use_pencil)
        self.pencil_button.pack(side=tk.LEFT)

        self.eraser_button = tk.Button(root, text="Ластик", command=self.use_eraser)
        self.eraser_button.pack(side=tk.LEFT)

        self.color_button = tk.Button(root, text="Цвет", command=self.choose_color)
        self.color_button.pack(side=tk.LEFT)

        self.clear_button = tk.Button(root, text="Очистить все", command=self.clear_all)
        self.clear_button.pack(side=tk.LEFT)

        # Создаем меню выбора стиля пера
        self.pen_style = tk.StringVar()
        self.pen_style.set("Сплошная линия")
        self.pen_style_menu = tk.OptionMenu(root, self.pen_style, "Сплошная линия", "Точечная линия", "Пунктирная линия")
        self.pen_style_menu.pack(side=tk.LEFT)

        # Воспроизводим музыкальный файл
        pygame.mixer.init()
        pygame.mixer.music.load("relax.mp3")
        pygame.mixer.music.play(-1)

    # Обработчик движения мыши
    def handle_motion(self, event):
        x, y = event.x, event.y
        if self.eraser_mode:
            if self.old_x and self.old_y:
                self.canvas.create_rectangle(x-10, y-10, x+10, y+10, fill="white", outline="white")
        else:
            if self.old_x and self.old_y:
                if self.pen_style.get() == "Сплошная линия":
                    self.canvas.create_line(self.old_x, self.old_y, x, y, fill=self.pen_color, width=2)
                elif self.pen_style.get() == "Точечная линия":
                    self.canvas.create_oval(x-1, y-1, x+1, y+1, fill=self.pen_color)
                elif self.pen_style.get() == "Пунктирная линия":
                    self.canvas.create_line(self.old_x, self.old_y, x, y, fill=self.pen_color, width=2, dash=(4, 4))
        self.old_x = x
        self.old_y = y

    # Обработчик отпускания кнопки мыши
    def handle_release(self, event):
        self.old_x, self.old_y = None, None

    # Обработчик клика мышью
    def handle_click(self, event):
        pass

    # Метод для установки режима карандаша
    def use_pencil(self):
        self.eraser_mode = False

    # Метод для установки режима ластика
    def use_eraser(self):
        self.eraser_mode = True

    # Метод для выбора цвета пера
    def choose_color(self):
        color = colorchooser.askcolor()[1]
        if color:
            self.pen_color = color

    # Метод для очистки холста
    def clear_all(self):
        self.canvas.delete("all")

# Создаем главное окно приложения и экземпляр класса DrawingApp
root = tk.Tk()
app = DrawingApp(root)
# Запускаем главный цикл обработки событий
root.mainloop()
