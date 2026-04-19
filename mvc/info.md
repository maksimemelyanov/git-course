# 📐 Архитектурные паттерны: MVC, MVP, MVVM

Разделение ответственности в приложениях на C#. Примеры, сравнение и практические сценарии.

В современной разработке на **C#** (WPF, ASP.NET Core, WinForms, MAUI, Xamarin) грамотное разделение логики, представления и данных — залог поддерживаемости и тестируемости кода. **MVC, MVP и MVVM** — три ключевых паттерна, каждый из которых решает задачу отделения UI от бизнес-логики по-своему. Рассмотрим их на конкретных примерах.

## 🔁 1. MVC (Model-View-Controller)

**Суть:** **Controller** обрабатывает ввод пользователя, обновляет **Model**, а **View** отображает данные из Model (часто через наблюдение).

**Поток:** Пользователь → Controller → Model → View (обновление). View не знает о Controller напрямую, но в классическом варианте Controller управляет выбором View.

**Где используется:** ASP.NET Core MVC, веб-фреймворки, классические desktop-реализации.

### 📌 Пример MVC на C# (консольная симуляция)

```
// ---------- Model ----------
public class UserModel
{
    public string Name { get; set; }
    public int Age { get; set; }
}

// ---------- View (интерфейс и реализация) ----------
public interface IUserView
{
    void DisplayUser(string name, int age);
    string GetInputName();
    int GetInputAge();
}

public class ConsoleUserView : IUserView
{
    public void DisplayUser(string name, int age) =>
        Console.WriteLine($"Пользователь: {name}, возраст: {age}");

    public string GetInputName()
    {
        Console.Write("Введите имя: ");
        return Console.ReadLine();
    }

    public int GetInputAge()
    {
        Console.Write("Введите возраст: ");
        return int.Parse(Console.ReadLine());
    }
}

// ---------- Controller ----------
public class UserController
{
    private UserModel _model;
    private IUserView _view;

    public UserController(UserModel model, IUserView view)
    {
        _model = model;
        _view = view;
    }

    public void UpdateUser()
    {
        // Получаем данные от View через Controller
        string name = _view.GetInputName();
        int age = _view.GetInputAge();

        // Обновляем Model
        _model.Name = name;
        _model.Age = age;

        // Обновляем View на основе Model
        _view.DisplayUser(_model.Name, _model.Age);
    }
}

// Использование (Program.cs)
// var model = new UserModel();
// var view = new ConsoleUserView();
// var controller = new UserController(model, view);
// controller.UpdateUser();
```

**Плюсы:** разделение логики, Controller управляет потоком. **Минусы:** View часто «толстая», Controller может обрастать логикой отображения.

## 🎤 2. MVP (Model-View-Presenter)

**Суть:** **Presenter** выступает посредником между View и Model. View максимально пассивна (передаёт события Presenter'у), Presenter обновляет View и Model.

**Отличие от MVC:** View не взаимодействует с Model напрямую; Presenter полностью управляет представлением. Идеально для Windows Forms и legacy UI.

**Варианты:** Passive View (View ничего не знает о Model) и Supervising Controller (небольшая привязка).

### 📌 Пример MVP на C# (интерфейс View + Presenter)

```
// ---------- Model ----------
public class WeatherModel
{
    public double GetTemperatureCelsius() => 22.5;
}

// ---------- View (интерфейс, который реализует форма) ----------
public interface IWeatherView
{
    string City { get; set; }
    void DisplayTemperature(string tempText);
    event Action UpdateWeather;   // событие, вызываемое при запросе
}

// ---------- Presenter ----------
public class WeatherPresenter
{
    private IWeatherView _view;
    private WeatherModel _model;

    public WeatherPresenter(IWeatherView view)
    {
        _view = view;
        _model = new WeatherModel();
        _view.UpdateWeather += OnUpdateWeather;
    }

    private void OnUpdateWeather()
    {
        double celsius = _model.GetTemperatureCelsius();
        string result = $"В городе {_view.City} температура: {celsius} °C";
        _view.DisplayTemperature(result);
    }
}

// Пример реализации View (WinForms/WPF допустим)
// public partial class WeatherForm : Form, IWeatherView
// {
//     public string City { get => txtCity.Text; set => txtCity.Text = value; }
//     public event Action UpdateWeather;
//     private void btnGetWeather_Click(object sender, EventArgs e) => UpdateWeather?.Invoke();
//     public void DisplayTemperature(string tempText) => lblResult.Text = tempText;
// }
```

**Плюсы:** высокая тестируемость Presenter, View предельно тупая (легко заменять). **Минусы:** много кода-связки (интерфейсы, события).

## ⚡ 3. MVVM (Model-View-ViewModel)

**Суть:** **ViewModel** — абстракция View, предоставляет данные и команды для привязки (data binding). View привязывается к свойствам ViewModel, которая реализует `INotifyPropertyChanged`.

**Где используется:** WPF, MAUI, Avalonia, WinUI, Xamarin.Forms. Самый мощный паттерн для XAML-технологий.

**Ключевые элементы:** Binding, Commands (ICommand), INotifyPropertyChanged.

### 📌 Пример MVVM на C# (базовый ViewModel + команда)

```
using System.ComponentModel;
using System.Runtime.CompilerServices;
using System.Windows.Input;

// ---------- Model ----------
public class ProductModel
{
    public string Title { get; set; }
    public decimal Price { get; set; }
}

// ---------- Базовый ViewModel ----------
public abstract class ViewModelBase : INotifyPropertyChanged
{
    public event PropertyChangedEventHandler PropertyChanged;
    protected void OnPropertyChanged([CallerMemberName] string prop = null) =>
        PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(prop));
}

// ---------- RelayCommand (упрощённая реализация ICommand) ----------
public class RelayCommand : ICommand
{
    private readonly Action _execute;
    private readonly Func _canExecute;
    public RelayCommand(Action execute, Func canExecute = null)
    {
        _execute = execute;
        _canExecute = canExecute;
    }
    public bool CanExecute(object parameter) => _canExecute == null || _canExecute();
    public void Execute(object parameter) => _execute();
    public event EventHandler CanExecuteChanged
    {
        add { CommandManager.RequerySuggested += value; }
        remove { CommandManager.RequerySuggested -= value; }
    }
}

// ---------- ViewModel для Product ----------
public class ProductViewModel : ViewModelBase
{
    private ProductModel _product;
    private string _statusMessage;

    public ProductViewModel()
    {
        _product = new ProductModel { Title = "Ноутбук", Price = 899.99m };
        BuyCommand = new RelayCommand(ExecuteBuy, CanBuy);
    }

    public string Title
    {
        get => _product.Title;
        set { _product.Title = value; OnPropertyChanged(); }
    }

    public decimal Price
    {
        get => _product.Price;
        set { _product.Price = value; OnPropertyChanged(); }
    }

    public string StatusMessage
    {
        get => _statusMessage;
        set { _statusMessage = value; OnPropertyChanged(); }
    }

    public ICommand BuyCommand { get; }

    private bool CanBuy() => Price > 0;
    private void ExecuteBuy()
    {
        StatusMessage = $"Вы купили {Title} за {Price:C}";
    }
}

// XAML-привязка (пример):
// <TextBlock Text="{Binding Title}" />
// <Button Command="{Binding BuyCommand}" Content="Купить" />
```

**Плюсы:** минимальная логика в View, мощный data binding, разделение через команды и уведомления. **Минусы:** сложность настройки (особенно для новичков), возможный over-engineering.

* * *

## 📊 Сравнительная таблица: когда что выбрать?

#### 🎯 MVC

**Платформы:** ASP.NET Core, веб, некоторые desktop  
**Тестируемость:** средняя (Controller можно тестировать)  
**Сложность:** низкая/средняя

#### 🎤 MVP

**Платформы:** WinForms, legacy UI, Xamarin.Forms (старый подход)  
**Тестируемость:** высокая (Presenter изолирован)  
**Сложность:** средняя (много интерфейсов)

#### ⚡ MVVM

**Платформы:** WPF, MAUI, Avalonia, UWP, WinUI  
**Тестируемость:** очень высокая (ViewModel чистая)  
**Сложность:** средняя/высокая (требует понимания binding)

### 🔍 Ключевые отличия (памятка разработчика)

*   **MVC:** Controller — точка входа. View отображает Model (иногда через Controller). Акцент на управлении потоком.
*   **MVP:** View вызывает Presenter; Presenter напрямую обновляет View через интерфейс. View максимально пассивна.
*   **MVVM:** View привязывается к свойствам ViewModel; используется автоматическое обновление UI (`INotifyPropertyChanged`) и команды (`ICommand`).

💡 **Совет для C# разработчиков:**

*   Для **ASP.NET Core** — естественный выбор MVC (или Minimal API, но идеология схожа).
*   Для **Windows Forms** — MVP упрощает тестирование и изоляцию логики.
*   Для **WPF / MAUI** — MVVM даёт максимальную гибкость, переиспользование и поддерживаемость.

## 📁 Пример организации кода (структура проекта)

```
// Для MVVM в WPF (типичная структура)
MyApp/
├── Models/
│   └── ProductModel.cs
├── ViewModels/
│   ├── BaseViewModel.cs
│   ├── ProductViewModel.cs
│   └── RelayCommand.cs
├── Views/
│   ├── ProductWindow.xaml
│   └── ProductWindow.xaml.cs

// Для MVP в WinForms
MyWinApp/
├── Models/
│   └── UserModel.cs
├── Presenters/
│   └── UserPresenter.cs
├── Views/
│   ├── IUserView.cs (интерфейс)
│   └── UserForm.cs (реализация)
```

## 🧪 Тестируемость: почему это важно?

Все три паттерна улучшают возможность модульного тестирования бизнес-логики без UI. Например, для MVVM достаточно создать экземпляр `ProductViewModel`, проверить команды и свойства. Для MVP — замокать `IWeatherView` и протестировать `WeatherPresenter`. В ASP.NET MVC — протестировать `Controller` с подменой зависимостей.

```
// Пример unit-теста для Presenter (MVP)
[Test]
public void UpdateWeather_Should_DisplayCorrectTemperature()
{
    // Arrange
    var mockView = new Mock<IWeatherView>();
    mockView.SetupProperty(v => v.City, "Москва");
    var presenter = new WeatherPresenter(mockView.Object);
    // Act
    mockView.Raise(v => v.UpdateWeather += null);
    // Assert
    mockView.Verify(v => v.DisplayTemperature(It.Is<string>(s => s.Contains("22.5"))), Times.Once);
}
```

* * *

## 🏁 Заключение

MVC, MVP и MVVM — не взаимоисключающие парадигмы, а эволюция разделения ответственности. В C#-экосистеме **MVVM** доминирует в современных XAML-фреймворках, **MVC** — в вебе (ASP.NET Core), а **MVP** остаётся надёжным выбором для WinForms и сложных настольных приложений с «толстым клиентом». Понимание всех трёх паттернов обогащает архитектурный кругозор и помогает выбирать оптимальное решение под задачу.
