# Лабораторная работа №4
## Цель лабораторной работы №4

1. Научиться синтаксису и принципам работы с массивами средствами языка C#.
2. Научиться реализовывать интерфейсы IComparable<T> и IEnumerator<T>.
3. Получить практические навыки работы с оператором yield.

### Задание №1
Создайте класс MyMatrix, представляющий матрицу m на n.
Создайте конструктор, принимающий число строк и столбцов, заполняющий матрицу случайными числами в диапазоне, который пользователь вводит при запуске программы.
Определите операторы сложения, вычитания и умножения матриц, а также умножения и деления матрицы на число.
Создайте пользовательский индексатор матрицы для доступа к элементам матрицы по номеру строки и столбца.


### Програмный код задания №1
    using System;

    class MyMatrix
    {
    private int[,] matrix;
    private int rows;
    private int cols;
    private Random random = new Random();

    public MyMatrix(int m, int n, int minValue, int maxValue)
    {
        rows = m;
        cols = n;
        matrix = new int[rows, cols];

        for (int i = 0; i < rows; i++)
        {
            for (int j = 0; j < cols; j++)
            {
                matrix[i, j] = random.Next(minValue, maxValue + 1);
            }
        }
    }

    public int this[int i, int j]
    {
        get { return matrix[i, j]; }
        set { matrix[i, j] = value; }
    }

    public static MyMatrix operator +(MyMatrix a, MyMatrix b)
    {
        if (a.rows != b.rows || a.cols != b.cols)
            throw new Exception("Matrices have different dimensions");

        MyMatrix result = new MyMatrix(a.rows, a.cols, 0, 0);

        for (int i = 0; i < a.rows; i++)
        {
            for (int j = 0; j < a.cols; j++)
            {
                result[i, j] = a[i, j] + b[i, j];
            }
        }

        return result;
    }

    public static MyMatrix operator -(MyMatrix a, MyMatrix b)
    {
        if (a.rows != b.rows || a.cols != b.cols)
            throw new Exception("Matrices have different dimensions");

        MyMatrix result = new MyMatrix(a.rows, a.cols, 0, 0);

        for (int i = 0; i < a.rows; i++)
        {
            for (int j = 0; j < a.cols; j++)
            {
                result[i, j] = a[i, j] - b[i, j];
            }
        }

        return result;
    }

    public static MyMatrix operator *(MyMatrix a, MyMatrix b)
    {
        if (a.cols != b.rows)
            throw new Exception("Matrices dimensions are not suitable for multiplication");

        MyMatrix result = new MyMatrix(a.rows, b.cols, 0, 0);

        for (int i = 0; i < a.rows; i++)
        {
            for (int j = 0; j < b.cols; j++)
            {
                for (int k = 0; k < a.cols; k++)
                {
                    result[i, j] += a[i, k] * b[k, j];
                }
            }
        }

        return result;
    }

    public static MyMatrix operator *(MyMatrix a, int scalar)
    {
        MyMatrix result = new MyMatrix(a.rows, a.cols, 0, 0);

        for (int i = 0; i < a.rows; i++)
        {
            for (int j = 0; j < a.cols; j++)
            {
                result[i, j] = a[i, j] * scalar;
            }
        }

        return result;
    }

    public static MyMatrix operator /(MyMatrix a, int divisor)
    {
        MyMatrix result = new MyMatrix(a.rows, a.cols, 0, 0);

        for (int i = 0; i < a.rows; i++)
        {
            for (int j = 0; j < a.cols; j++)
            {
                result[i, j] = a[i, j] / divisor;
            }
        }

        return result;
    }

    public void Print()
    {
        for (int i = 0; i < rows; i++)
        {
            for (int j = 0; j < cols; j++)
            {
                Console.Write($"{matrix[i, j]} ");
            }
            Console.WriteLine();
        }
    }

    static void Main(string[] args)
    {
        int m, n, minValue, maxValue;
        Console.WriteLine("Enter the number of rows:");
        m = int.Parse(Console.ReadLine());

        Console.WriteLine("Enter the number of columns:");
        n = int.Parse(Console.ReadLine());

        Console.WriteLine("Enter the minimum value for random numbers:");
        minValue = int.Parse(Console.ReadLine());

        Console.WriteLine("Enter the maximum value for random numbers:");
        maxValue = int.Parse(Console.ReadLine());

        MyMatrix matrixA = new MyMatrix(m, n, minValue, maxValue);
        MyMatrix matrixB = new MyMatrix(m, n, minValue, maxValue);

        Console.WriteLine("Matrix A:");
        matrixA.Print();

        Console.WriteLine("Matrix B:");
        matrixB.Print();

        Console.WriteLine("Sum of matrices:");
        (matrixA + matrixB).Print();

        Console.WriteLine("Difference of matrices:");
        (matrixA - matrixB).Print();

        Console.WriteLine("Product of matrices:");
        (matrixA * matrixB).Print();

        int scalar = 5;
        Console.WriteLine($"Matrix A multiplied by {scalar}:");
        (matrixA * scalar).Print();

        int divisor = 2;
        Console.WriteLine($"Matrix A divided by {divisor}:");
        (matrixA / divisor).Print();
    }
    }

### Задание №2
Создайте класс Car с тремя авто-свойствами: Name, ProductionYear и MaxSpeed, соответствующими названию, году выпуска и максимальной скорости соответственно.
Создайте класс CarComparer, реализует IComparer<Car> и реализуйте метод Compare таким образом, чтобы можно было сортировать массив элементов Car по названию, году выпуска или максимальной скорости по выбору.
Создайте массив элементов Car и продемонстрируйте сортировку различными способами.


### Програмный код задания №2
    using System;
    using System.Collections;
    using System.Collections.Generic;

    // Класс, представляющий автомобиль
    public class Car
    {
    public string Name { get; set; }
    public int ProductionYear { get; set; }
    public int MaxSpeed { get; set; }
    }

    // Класс для сравнения автомобилей
    public class CarComparer : IComparer<Car>
    {
    private string sortBy;

    public CarComparer(string sortBy)
    {
        this.sortBy = sortBy;
    }

    public int Compare(Car x, Car y)
    {
        switch (sortBy)
        {
            case "Name":
                return string.Compare(x.Name, y.Name);
            case "ProductionYear":
                return x.ProductionYear.CompareTo(y.ProductionYear);
            case "MaxSpeed":
                return x.MaxSpeed.CompareTo(y.MaxSpeed);
            default:
                throw new ArgumentException("Invalid sort option");
        }
    }
    }

    class Program
    {
    static void Main()
    {
        Car[] cars = new Car[]
        {
            new Car { Name = "Toyota", ProductionYear = 2020, MaxSpeed = 180 },
            new Car { Name = "BMW", ProductionYear = 2018, MaxSpeed = 200 },
            new Car { Name = "Audi", ProductionYear = 2019, MaxSpeed = 190 }
        };

        // Сортировка по названию
        Array.Sort(cars, new CarComparer("Name"));
        Console.WriteLine("Сортировка по названию:");
        foreach (var car in cars)
        {
            Console.WriteLine($"{car.Name}, {car.ProductionYear}, {car.MaxSpeed}");
        }

        // Сортировка по году выпуска
        Array.Sort(cars, new CarComparer("ProductionYear"));
        Console.WriteLine("\nСортировка по году выпуска:");
        foreach (var car in cars)
        {
            Console.WriteLine($"{car.Name}, {car.ProductionYear}, {car.MaxSpeed}");
        }

        // Сортировка по максимальной скорости
        Array.Sort(cars, new CarComparer("MaxSpeed"));
        Console.WriteLine("\nСортировка по максимальной скорости:");
        foreach (var car in cars)
        {
            Console.WriteLine($"{car.Name}, {car.ProductionYear}, {car.MaxSpeed}");
        }
    }
    }

### Задание №3
Используйте класс Car из задания №2, на его основе создайте класс CarCatalor, содержащий массив элементов типа Car. 
Для класса CarCatalog реализуйте возможность итерации по элементам массива Car с помощью оператора foreach различными способами: 
Прямой проход с первого элемента до последнего.
Обратный проход от последнего к первому.
Проход по элементам массива с фильтром по году выпуска.
Проход по элементам массива с фильтром по максимальной скорости.

Примечание: для выполнения задания необходимо реализовать различные итераторы, используя конструкцию yield return. Для п.3 и 4, итератор должен принимать год выпуска и скорость как параметр, чтобы возвращать только те элементы коллекции, которые удовлетворяют условию.


### Програмный код задания №3
    using System;
    using System.Collections;
    using System.Collections.Generic;

    // Класс, представляющий автомобиль
    public class Car
    {
    public string Name { get; set; }
    public int ProductionYear { get; set; }
    public int MaxSpeed { get; set; }
    }

    // Класс, представляющий каталог автомобилей
    public class CarCatalog : IEnumerable<Car>
    {
    private Car[] cars;

    public CarCatalog(Car[] cars)
    {
        this.cars = cars;
    }

    // Прямой проход с первого элемента до последнего
    public IEnumerator<Car> GetEnumerator()
    {
        for (int i = 0; i < cars.Length; i++)
        {
            yield return cars[i];
        }
    }

    // Обратный проход от последнего к первому
    public IEnumerable<Car> GetReverseEnumerator()
    {
        for (int i = cars.Length - 1; i >= 0; i--)
        {
            yield return cars[i];
        }
    }

    // Проход по элементам массива с фильтром по году выпуска
    public IEnumerable<Car> GetByProductionYearEnumerator(int year)
    {
        foreach (Car car in cars)
        {
            if (car.ProductionYear == year)
            {
                yield return car;
            }
        }
    }

    // Проход по элементам массива с фильтром по максимальной скорости
    public IEnumerable<Car> GetByMaxSpeedEnumerator(int speed)
    {
        foreach (Car car in cars)
        {
            if (car.MaxSpeed == speed)
            {
                yield return car;
            }
        }
    }

    IEnumerator IEnumerable.GetEnumerator()
    {
        return GetEnumerator();
    }
    }

    class Program
    {
    static void Main()
    {
        Car[] cars = new Car[]
        {
            new Car { Name = "Toyota", ProductionYear = 2020, MaxSpeed = 180 },
            new Car { Name = "BMW", ProductionYear = 2018, MaxSpeed = 200 },
            new Car { Name = "Audi", ProductionYear = 2015, MaxSpeed = 190 }
        };

        CarCatalog catalog = new CarCatalog(cars);

        foreach (Car car in catalog)
        {
            Console.WriteLine(car.Name);
        }

        foreach (Car car in catalog.GetReverseEnumerator())
        {
            Console.WriteLine(car.Name);
        }

        foreach (Car car in catalog.GetByProductionYearEnumerator(2018))
        {
            Console.WriteLine(car.Name);
        }

        foreach (Car car in catalog.GetByMaxSpeedEnumerator(190))
        {
            Console.WriteLine(car.Name);
        }
    }
    }
