using System;
using System.Threading;

namespace LightingControl
{
    class Program
    {
        // Замените эти значения на ваши фактические порты и адреса.
        private const string COM_PORT = "COM3"; // Порт для связи с контроллером освещения
        private const string DEVICE_ADDRESS = "192.168.1.10"; // IP-адрес контроллера освещения

        static void Main(string[] args)
        {
            Console.WriteLine("Добро пожаловать в систему управления освещением!");

            // Инициализация контроллера освещения (обработчик событий)
            LightingController controller = new LightingController(COM_PORT, DEVICE_ADDRESS);
            controller.OnStateChanged += Controller_OnStateChanged;

            while (true)
            {
                Console.WriteLine("\nВыберите действие:");
                Console.WriteLine("1. Включить свет");
                Console.WriteLine("2. Выключить свет");
                Console.WriteLine("3. Установить яркость");
                Console.WriteLine("4. Изменить цвет");
                Console.WriteLine("5. Выйти");

                string choice = Console.ReadLine();

                switch (choice)
                {
                    case "1":
                        controller.TurnOn();
                        break;
                    case "2":
                        controller.TurnOff();
                        break;
                    case "3":
                        Console.Write("Введите желаемую яркость (0-100): ");
                        if (int.TryParse(Console.ReadLine(), out int brightness))
                        {
                            controller.SetBrightness(brightness);
                        }
                        else
                        {
                            Console.WriteLine("Неверный ввод.");
                        }
                        break;
                    case "4":
                        Console.Write("Введите желаемый цвет (RGB, например, 255,0,0 для красного): ");
                        string[] colorParts = Console.ReadLine().Split(',');
                        if (colorParts.Length == 3 && 
                            int.TryParse(colorParts[0], out int red) &&
                            int.TryParse(colorParts[1], out int green) &&
                            int.TryParse(colorParts[2], out int blue))
                        {
                            controller.SetColor(red, green, blue);
                        }
                        else
                        {
                            Console.WriteLine("Неверный ввод.");
                        }
                        break;
                    case "5":
                        Environment.Exit(0);
                        break;
                    default:
                        Console.WriteLine("Неверный выбор. Попробуйте снова.");
                        break;
                }
            }
        }

        // Обработчик события изменения состояния освещения
        private static void Controller_OnStateChanged(object sender, LightingStateChangedEventArgs e)
        {
            Console.WriteLine($"Состояние освещения: {e.State}");
        }
    }

    // Класс для управления освещением
    public class LightingController
    {
        private string _comPort;
        private string _deviceAddress;
        // Добавьте здесь методы для связи с контроллером освещения
        // (например, отправка команд по COM-порту или HTTP-запросов).

        public event EventHandler<LightingStateChangedEventArgs> OnStateChanged;

        public LightingController(string comPort, string deviceAddress)
        {
            _comPort = comPort;
            _deviceAddress = deviceAddress;
        }

        public void TurnOn()
        {
            // Отправка команды включения
            Console.WriteLine("Включено");
            // ...

            // Вызов события изменения состояния
            OnStateChanged?.Invoke(this, new LightingStateChangedEventArgs(true));
        }

        public void TurnOff()
        {
            // Отправка команды выключения
            Console.WriteLine("Выключено");
            // ...

            // Вызов события изменения состояния
            OnStateChanged?.Invoke(this, new LightingStateChangedEventArgs(false));
        }

        public void SetBrightness(int brightness)
        {
            // Отправка команды установки яркости
            Console.WriteLine($"Яркость: {brightness}");
          

            // Вызов события изменения состояния
            // (по желанию, можно передать brightness в событие)
            OnStateChanged?.Invoke(this, new LightingStateChangedEventArgs(true));
        }

        public void SetColor(int red, int green, int blue)
        {
            // Отправка команды установки цвета
            Console.WriteLine($"Цвет: R: {red}, G: {green}, B: {blue}");
            // ...

            // Вызов события изменения состояния
            // (по желанию, можно передать цвет в событие)
            OnStateChanged?.Invoke(this, new LightingStateChangedEventArgs(true));
        }
    }

    // Аргументы события изменения состояния освещения
    public class LightingStateChangedEventArgs : EventArgs
    {
        public bool State { get; } // True - включено, False - выключено

        public LightingStateChangedEventArgs(bool state)
        {
            State = state;
        }
    }
}
