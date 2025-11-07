#include <iostream>
#include "windows.h"
//библиотеки для чтения и записи информации в файлы
#include <fstream> 

enum class ValueQuality
{
    мусор = 5, обычное, редкое, мифическое, легендарное //эквивалент перечислению чисел
};

using namespace std;

/*
class Treasure //по умолчанию все внутри паблик
{
public:
    string name{ "добыча" };
    string valueQuality[5]{ "мусор", "обычное", "редкое", "мифическое", "легендарное" };
    string quality = valueQuality[0];
    unsigned int price{ 0 };
};
*/

struct Treasure //по умолчанию все внутри private
{
    string name{ "добыча" };
    ValueQuality quality = ValueQuality::мусор;
    unsigned int price{ 0 };
    Treasure(ValueQuality quality)
    {
        switch (quality)
        {
        case ValueQuality::мусор:
            cout << "качество плохое\n";
            break;
        case ValueQuality::обычное:
            cout << "качество средненькое\n";
            break;
        case ValueQuality::редкое:
            cout << "качество хорошее\n";
            break;
        case ValueQuality::мифическое:
            cout << "качество крутое\n";
            break;
        case ValueQuality::легендарное:
            cout << "качество идеальное\n";
            break;
        default:
            break;
        }
    }
};

struct Cloth : Treasure //наследование по умолчанию public
{
    Cloth(ValueQuality quality) : Treasure(quality) {};
    string valueSite[5]{ "обувь", "перчатки", "шлем", "нагрудник", "пояс"};
    string site{ NULL };
    unsigned short armor{ 1 };
};


//модификаторы доступа:
// private - приватный, запрещает доступ к свойствам и классам
//           за пределами самого класса
// protected - защищенный, можно передавать свойства и методы
//        в классы наследники, но все еще нельзя использовать
//        в основном потоке программы
// public - публичный, общедоступный, можно использовать везде

//базовый класс - абстрактный (класс у которого все методы виртуальные)
class Npc
{
public: //модификатор 0 защищенный (дает доступ внутри класса родителя и наследника)
    //но все еще не дает доступ в основном потоке программы
    string name{ "персонаж" };
    unsigned int health{ 10 };
    float damage{ 5 };
    unsigned short lvl{ 1 };
public: //публичный модификатор доступ (использовать метод можно в любом месте)
    virtual void GetInfo() //метод класса
    {
        cout << "Имя - " << name << endl;
        cout << "Здоровье - " << health << endl;
        cout << "Урон - " << damage << endl;
    }
    virtual void Create() {};
};

//наследование - использование доступных свойств
// и методов класса родителям (parent), классом наследником(child)
class Warrior : public virtual Npc //наследование с модификатором доступа public
{
protected:  //модификатор 0 (приватный - защищенный, доступ к полям, только внутри класса)
    unsigned short strenght{ 31 };
    string weapons[4] = { "кастет", "дубинка", "клинок", "меч" };
public:
    //конструктор - метод, который вызывается в момент создания экземпляра
    //класса (вручную вызвать в основном потоке программы не можем)

    Warrior() //конструктор по умолчанию, когда нет аргументов
    {
        name = "воин";
        health = 35;
        damage = 10;
    }
    //кастомный конструктор
    Warrior(string name, unsigned int health, float damage)
    {
        cout << "кастомный конструктор война" << endl;
        this->name = name;
        this->health = health;
        this->damage = damage;
    }

    void GetWeapons()
    {
        cout << name << " взял в руки " << weapons[lvl - 1];
    }
    void GetInfo() override  //полиморфизм (перегрузка для метода)
    {
        Npc::GetInfo();
        cout << "Сила - " << strenght << endl;
        cout << "Доступное оружие - ";
        for (int i = 0; i < lvl; i++)
        {
            cout << weapons[i] << endl;
        }
    }
    void Create() override
    {
        cout << "Вы создали война" << endl;
        cout << "Введите имя персонажа\t";
        cin >> name;
        GetInfo();
        GetWeapons();
    }
    //перегрузка операторов
    //перегрузка оператора сравнения (==)
    bool operator == (const Warrior& warrior) const
    {
        return ((warrior.damage == this->damage) && (warrior.health == this->health)
            && (warrior.strenght == this->strenght));
    }


    //деструктор - метод, который вызывается автоматически при высвобождении памяти
    //при окончании работы с экземпляром класса (нельзя вызвать вручную)
    ~Warrior() //деструктор всегда без аргументов
    {
        cout << name << " пал смертью храбрых" << endl;
    }
};


//virtual - создает виртуализацию методов, классов
//при этом сам класс повторно не создается
class Wizard : public virtual Npc
{
protected:
    unsigned short intellect = 27;
    string spell[4] = { "вспышка", "магисческая стрела", "огненный шар", "метеоритный дождь" };
public:
    Wizard()
    {
        name = "волшебник";
        health = 23;
        damage = 15;
    }
    Wizard(string name, unsigned int health, float damage)
    {
        cout << "кастомный конструктор волшебника" << endl;
        this->name = name;
        this->health = health;
        this->damage = damage;
    }
    void GetInfo() override  //полиморфизм (перегрузка для метода)
    {
        Npc::GetInfo();
        cout << "Интеллект - " << intellect << endl;
        cout << "Доступные заклинания в книге заклинаний - ";
        for (int i = 0; i < lvl; i++)
        {
            cout << spell[i] << endl;
        }
    }
    void CastSpell()
    {
        cout << name << " применяет " << spell[lvl - 1] << endl;
    }
    void Create() override
    {
        cout << "Вы создали волшебника" << endl;
        cout << "Введите имя персонажа\t";
        cin >> name;
        GetInfo();
        CastSpell();
    }
    //перегрузка оператора суммирование
    Wizard operator + (const Wizard& wizard) const
    {
        return Wizard(
            this->name,
            (this->health + wizard.health),
            (this->damage + wizard.damage)
        );
    }

    ~Wizard() //деструктор всегда без аргументов
    {
        cout << name << " испустил дух" << endl;
    }
};

//множественное наследование
class Paladin : public Warrior, public Wizard
    //следующий родительственный класс добавляется через запятую
{
public:
    Paladin()
    {
        name = "паладин";
        health = 25;
        damage = 12;
        strenght = 27;
    }
    void GetInfo() override
    {
        Warrior::GetInfo();
        cout << "Интеллект - " << intellect << endl;
        cout << "Доступные заклинания в книге заклинаний - ";
        for (int i = 0; i < lvl; i++)
        {
            cout << spell[i] << endl;
        }
    }
    void Create() override
    {
        cout << "Вы создали паладина" << endl;
        cout << "Введите имя персонажа\t";
        cin >> name;
        GetInfo();
        CastSpell();
        GetWeapons();
    }
};

class Player
{
public:
    void Create(Npc* player)
    {
        player->Create();
    }
};

int main()
{
    setlocale(LC_ALL, "Rus");
    SetConsoleCP(1251);
    SetConsoleOutputCP(1251);

    Cloth cloth(ValueQuality::мифическое);
    cloth.armor = 10;
    cloth.site = cloth.valueSite[2];
    cloth.name = "Шлем властелина подземелий";
    cloth.price = 50;
    cout << cloth.name << '\n' << cloth.site << '\n' << cloth.armor << '\n' << cloth.price << '\n';

    Warrior* warrior = new Warrior();
    Warrior* warrior2 = new Warrior();

    cout << (*warrior == *warrior2) << endl;

    Wizard* wizard1 = new Wizard();
    Wizard* wizard2 = new Wizard();
    Wizard* megaWizard = new Wizard();
    *megaWizard = *wizard1 + *wizard2;
    megaWizard->GetInfo();

    *megaWizard = *wizard1 + *wizard2;
    megaWizard->GetInfo();


    Paladin* paladin = new Paladin();
    Player* player = new Player();

    cout << "Привет, путник\nПрисядь у костра и расскажи о себе\n";
    cout << "Ты впервые тут? (1 - новый персонаж, 2 - загрузить)\n";
    unsigned short choise = 1;
    //создать уникальную функцию для проверки условия выбора
    cin >> choise;
    while (choise > 2 || choise < 1)
    {
        cout << "Наверное ты ошибся, повтори снова\n";
        cin >> choise;
    }
    if (choise == 1)
    {
        cout << "Расскажи о своих навыках\n\t1 - Воин\n\t2 - Волшебник\n\t3 - Паладин)";
        //-----------
        // тут уже будет вызвана ваша красивая функция
        cin >> choise;
        while (choise > 3 || choise < 1)
        {
            cout << "Такого еще не было в наших краях\nНе мог бы ты повторить\n";
            cin >> choise;
        }
        //_____________

    /*switch (choise)
    {
    case 1: {
      player->Create(&warrior);
      delete wizard;
      delete paladin;
      break; }
    case 2: {
      player->Create(&wizard);
      delete warrior;
      delete paladin;
      break; }
    case 3: {
      player->Create(&paladin);
      delete warrior;
      delete wizard;
      break; }
    }*/
    }
    else
    {
        ifstream loadSystem("save.txt", ios::binary);
        if (!loadSystem.is_open())
        {
            cout << "Связь с космосом потеряна\nПамять о ваших прошлых путешествиях повреждена\n";
        }
        else
        {
            loadSystem.read(reinterpret_cast<char*>(&(paladin->name)), sizeof(paladin->name));
            loadSystem.read(reinterpret_cast<char*>(&(paladin->health)), sizeof(paladin->health));
            loadSystem.read(reinterpret_cast<char*>(&(paladin->damage)), sizeof(paladin->damage));
            loadSystem.read(reinterpret_cast<char*>(&(paladin->lvl)), sizeof(paladin->lvl));
            paladin->GetInfo();
        }
    }
    cout << "Сделаем остановку тут?\n\t1 - сохранить игру\n\t2 - продолжить\n";
    cin >> choise;
    while (choise > 2 || choise < 1)
    {
        cout << "Нужно четко определиться с решением\nПовтори свой ответ\n";
        cin >> choise;
    }
    ofstream saveSystem("save.txt");
    if (!saveSystem.is_open())
    {
        cout << "Ошибка сохранения\nПопробуйте позже\n";
    }
    else
    {
        paladin->damage = 99;
        paladin->health = 99;
        paladin->lvl = 99;
        saveSystem.write(reinterpret_cast<const char*>(&(paladin->name)), sizeof(paladin->name));
        saveSystem.write(reinterpret_cast<const char*>(&(paladin->health)), sizeof(paladin->health));
        saveSystem.write(reinterpret_cast<const char*>(&(paladin->damage)), sizeof(paladin->damage));
        saveSystem.write(reinterpret_cast<const char*>(&(paladin->lvl)), sizeof(paladin->lvl));
    }

    return 0;
}
