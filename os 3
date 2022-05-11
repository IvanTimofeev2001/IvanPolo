using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading;
using System.Threading.Tasks;

namespace OS3 {
    class Program {
        static readonly int nQueue = 200; //Размер очереди
        static Random randomNum; //Генерация случайных чисел
        static Queue < int > queue; //Очередь
        static Thread[] manufacturers; //Массив потоков производителей
        static Thread[] consumers; //Массив потоков потребителей
        static Mutex mutex; //Мьютекс для работы с потоками и циклами
        static bool manufacturersIsOver; //Пора останавливать потоки производителей и запускать потоки потребителей
        //Метод для потоков производителей
        static void Manufacturer() {
            int value;
            while (!manufacturersIsOver) {
                mutex.WaitOne(); //Синхронизация потоков. Выполняется для корректной работы с очередью
                if (queue.Count < nQueue) {
                    value = randomNum.Next(1, 100);
                    queue.Enqueue(value);
                    Console.WriteLine(Thread.CurrentThread.Name + "В конец очереди добавленно число " + value + "\nДлина очереди: " + queue.Count + "\n");
                }
                mutex.ReleaseMutex(); //Возвращение параллельной работы потоков
            }
            Console.WriteLine(Thread.CurrentThread.Name + "Закончил работу\n");
        }
        static void Dequeue() {
            mutex.WaitOne(); //Синхронизация потоков(на момент queue.Count поток знает, допустим, что в очереди остался один элемент, а другой поток уже успел удалить, при попытке удаления пустого эл-та = ошибка)
            if (queue.Count > 0) {
                int value = queue.Dequeue();
                Console.WriteLine(Thread.CurrentThread.Name + "Из начала очереди удаленно число " + value + "\nДлина очереди: " + queue.Count + "\n");
            }
            mutex.ReleaseMutex(); //Возвращение параллельной работы потоков
        }
        //Метод для потоков потребителей
        static void Consumer() {
            while (true) {
                if (!manufacturersIsOver) {
                    if (queue.Count >= 100 || queue.Count == 0) {
                        Thread.Sleep(1000);
                    } else if (queue.Count <= 80) {
                        Dequeue();
                    }
                } else if (queue.Count == 0) {
                    break;
                } else {
                    Dequeue();
                }
            }
            Console.WriteLine(Thread.CurrentThread.Name + "Закончил работу\n");
        }
        static void Main(string[] args) {
            int nGenerator = 3, nConsumer = 2; //Количество потоков производителей и потребителей
            manufacturersIsOver = false;
            queue = new Queue < int > ();
            mutex = new Mutex();
            randomNum = new Random();
            manufacturers = new Thread[nGenerator];
            consumers = new Thread[nConsumer];
            Console.WriteLine("Нажмите 'q', чтобы остановить потоки производителей.");
            for (int i = 0; i < nGenerator; i++) {
                manufacturers[i] = new Thread(new ThreadStart(Manufacturer));
                manufacturers[i].Name = "Производитель #" + (i + 1) + "\n";
                manufacturers[i].Start();
            }
            for (int i = 0; i < nConsumer; i++) {
                consumers[i] = new Thread(new ThreadStart(Consumer));
                consumers[i].Name = "Потребитель #" + (i + 1) + "\n";
                consumers[i].Start();
            }
            char key = ' ';
            while (key != 'q') {
                key = (char) Console.Read();
            }
            manufacturersIsOver = true;
            Console.ReadKey();
        }
    }
}
