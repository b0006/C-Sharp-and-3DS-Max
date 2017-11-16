# C# + MaxScript
С помощью C# формируем MaxScript и визуализируем данные в 3DS Max
<h2>Содержание</h2>
<ul>
  <li><a href="#task">Задача;</a></li>
  <li><a href="#theory">Немного теории об алгоритме компоновки;</a></li>
  <li><a href="#net">Немного о NET-файле;</a></li>
  <li><a href="#sharp">Код С#;</a></li>
  <li><a href="#max">Код MaxScript;</a></li>
  <li><a href="#result">Результат работы.</a></li>
</ul>

<h2 id="task">Задача</h2>
<p align="justify">
Поставлена задача применить алгоритм компоновки печатной платы. Сам алгоритм применить не сложно, он последовательный, что упрощает процесс кодирования.
</p>

<p align="justify">
На вход программе подается некоторый NET-файл, который представляет из себя текстовый файл. В нем хранится названия элементов печатной платы; и какой номер пина элемента подключен к той или иной цепи.
</p>

<p align="justify">
В данной программе используется последовательный алгоритм копмпоновки с применением матрицы цепей. Исходя из данных NET-файла строится матрица цепей. И на ее основе реализуется алгоритм компоновки. Далее следует визуализировать результат применения алгоритма компоновки.
</p>

<p align="justify">
Но возникает вопрос: "А как визуализировать результат компоновки?". Так как стоит задача только компоновки элементов, а не трассировки, то визуализировать компоновку без трассировки в 2D пространстве не трудно, но результат будет оставлять желать лучшего: без трассировки линии соединения будет "проложены" напрямую, что сказывается на восприятии компоновки в целом для пользователя.
</p>

<p align="justify">
Но что мешает визуализировать в 3D?
</p>

<h2 id="theory">Немного теории об алгоритме компоновки</h2>

<p align="justify">
Принципиальные электрические схемы соединения многоконтактных элементов (например, микросхем и микросборок) удобно представлять гиперграфом Q и записывать в виде матрицы цепей:
</p>

<p>
<img src="https://img-fotki.yandex.ru/get/477464/49342033.0/0_23bb14_3fa750ed_orig.png" width="172" height="46" border="0" title="1.PNG" alt="1.PNG"/>
</p>

<p align="justify">
где m — число элементов схемы; k — число выводов многоконтакт-ных элементов.
</p>

<p align="justify">
Элемент Сij матрицы С означает номер электрической цепи, которая подключается к j-му выводу i-го многоконтактного элемента. Если контакт свободен, то Сij = 0. Оптимальному разбиению схемы на n частей G1, G2. …, Gn соответствует такое разбиение матрицы С на подматрицы С1. С2. .... Сn, при котором число связей между частями минимально. Критерий оптимальности в этом случае записывается в виде:
</p>

<p>
<img src="https://img-fotki.yandex.ru/get/772910/49342033.0/0_23bb15_901896b1_orig.png" width="489" height="78" border="0" title="2.PNG" alt="2.PNG"/>
</p>

<p>
где Rij — число связей между i-й и j-й частями.
</p>

<p align="justify">
Работа алгоритма заключается в поочередном определении для каждого элемента Ui схемы чисел ∆R<sub>1</sub><sup>i</sup>, ∆R<sub>2</sub><sup>i</sup>…∆R<sub>n</sub><sup>i</sup>, характе¬ризующих приращение связей при расположении этого элемента в части G1, G2…Gn. Для определения этих чисел вводится вспо¬могательная матрица W = || wij ||<sub>n x l</sub>
</p>
<p>
где l — число цепей схемы; n — число частей разбиения.
</p>

<p>
Элемент матрицы Wij определяется соотношением:
</p>

<p>
<img src="https://img-fotki.yandex.ru/get/962386/49342033.0/0_23bb16_f0688493_orig.png" width="541" height="67" border="0" title="3.PNG" alt="3.PNG"/>
</p>

<p align="justify">
Число ∆R<sub>j</sub><sup>i</sup> равно числу единиц l-вектора строки W<sub>j</sub><sup>i</sup>, рассчитываемой во формуле:
</p>

<p>
<img src="https://img-fotki.yandex.ru/get/962386/49342033.0/0_23bb17_6a041f4_orig.png" width="323" height="129" border="0" title="4.PNG" alt="4.PNG"/>
</p>

<p align="justify">
где W<sup>v</sup> – v-я строка матрицы W; "не" Wj - это инверсия строки Wj; W<sup>0</sup><sub>i</sub>- l-вектор-строка, заполненная в соответствии с предыдущей формулой.
</p>

<p align="justify">
Из чисел ∆R<sub>1</sub><sup>i</sup>, ∆R<sub>2</sub><sup>i</sup>…∆R<sub>n</sub><sup>i</sup> выбирается минимальное, и элемент Ui заносится в соответствующую часть.
</p>

<h2 id="net">Немного о NET-файле</h2>

<p>
Как уже было указано выше, на вход программы подается некий NET-файл. Для представления, он представлен ниже:
</p>

```
114      DD3('14) DD5('14) DD2('14) DD4('14);
107      DD3('7) DD5('7) DD2('7) DD4('7);
N14638   DD2('2) DD1('2);
N14853   DD5('9) DD1('6);
N14910   DD5('8) DD5('12) C1('1);
N14929   DD1('9) DD2('3) DD5('10) DD5('11) DD2('11);
N15071   DD3('2) DD2('5) DD3('4);
N15202   DD3('3) DD4('1);
N15323   DD4('2) DD3('8);
N15558   DD5('2) DD3('6) X1('4);
N15501   DD5('5) DD3('11);
N16125   DD3('12) DD3('10) DD2('8);
N16620   DD3('5);
N17194   DD3('1) DD3('13);
N18175   DD1('1) R1('2) X1('2);
N18326   DD1('3) R1('4) X1('1);
N18572   DD2('1) R1('9) DD2('13) DD2('10);
N19488   DD2('4);
N16039   DD2('12) DD3('9) DD1('4);
N24212   DD5('1) DD1('8) DD5('4) X1('7);
N25453   DD4('3) DD1('5);
N30469   R1('1) X1('3);
N32456   DD5('3) X1('5);
N32952   X1('6) DD5('6);
N34768   R1('8) X1('8);
N35485   X1('9);
N37663   C1('2) DD5('13) R1('6);
N39046   R1('5) X1('10);

```

<p>
Рассмотрим первую строку:
<ul>
  <li>Название цепи: 114;</li>
  <li>Элементы данной цепи: DD3, DD5, DD2, DD4;</li>
  <li>Номера пинов: у каждого элемента номер пина равен 14.</li>
</ul>
</p>

<p>
Есть еще один вариант NET-файла:
</p>

```
$PACKAGES
K10-17! 0.1 mk; C1
K10-17! 200mk; C2
K561LE5! K561LE5; DD1
MLT! 100k; R1
MLT! 10k; R2
MLT! 100k; R3
MLT! 1M; R4
MLT! 51k; R5
D814B! D814B; VD1
KD522B! KD522B; VD2
KD522B! KD522B; VD3
KD522B! KD522B; VD4
KD522B! KD522B; VD5
IRF840! IRF840; VT1
SNP! SNP; X1
$NETS
7;  DD1.7 X1.1 VD5.1 C1.1,
     VD1.1 C2.1 VT1.2
14;  DD1.14 C2.2 VD3.2 VD4.2,
     X1.2
N00745;  R1.1 R2.2
N00845;  R2.1 C1.2 VD2.1 R3.1
N01136;  DD1.5 DD1.6 R4.1 R3.2
N01222;  R4.2 DD1.3
N01254;  DD1.4 DD1.12 DD1.13 DD1.2,
     DD1.1 DD1.9 DD1.8
N01026;  VD1.2 VD3.1 R5.1 R1.2,
     VD2.2
N00990;  VT1.1 VD5.2 DD1.11 VD4.1,
     DD1.10
N01748;  X1.3 VT1.3
N01774;  R5.2 X1.4
$END
```

<p>
Здесь нам необходимо всё от $NETS до $END. Остальное нас не интересует. И программа должна уметь парсить два варианта NET-файла.
</p>

<h2 id="sharp">Код C#</h2>

<p align="justify">
Программа реализована на основе WPF. В идеале стоило применить какой-нибудь паттерн, который разделяет представление (View) и модель (Model), но логику данного кода не сложно "подогнать", допустим, в консольную версию программы.
</p>

<p>
Создадим класс Schema (схема), в котором имеются поля:
<ul>
  <li>название цепи (chainName);</li>
  <li>название элемента (elemName);</li>
  <li>номер пина элемента (elemPin).</li>
</ul>
</p>

```csharp
class Schema
{
    private IEnumerable<string> chainName = new List<string>();
    private IEnumerable<string> elemName = new List<string>();
    private IEnumerable<int> elemPin = new List<int>();

    public IEnumerable<string> СhainName
    {
        get { return chainName; }
        set { chainName = value; }
    }

    public IEnumerable<string> ElemName
    {
        get { return elemName; }
        set { elemName = value; }
    }

    public IEnumerable<int> ElemPin
    {
        get { return elemPin; }
        set { elemPin = value; }
    }

    public IEnumerable<string> setChainName(string value)
    {
        chainName = chainName.Concat(new[] { value });
        return chainName;
    }

    public IEnumerable<string> setElemName(string value)
    {
        elemName = elemName.Concat(new[] { value });
        return elemName;
    }

    public IEnumerable<int> setElemPin(string value)
    {
        elemPin = elemPin.Concat(new[] { Convert.ToInt32(value) });
        return elemPin;
    }
}
```

<p>
Далее необходимо запарсить NET-файл.
</p>

```csharp
//читаем NET файл
public List<string> readNetFile()
{
    OpenFileDialog dlg = new OpenFileDialog();
    dlg.FileName = "Document";
    dlg.DefaultExt = ".net";
    dlg.Filter = "NET file (.net)|*.net";

    // открыть диалоговое окно выбора файла
    Nullable<bool> result = dlg.ShowDialog();

    //здесь будет результат
    List<string> split = new List<string>();

    // Если файл прочитан
    if (result == true)
    {
        // открыть файл
        string filename = dlg.FileName;

        //чтение NET файла 
        string textNetFile = "";

        try
        {
            using (StreamReader sr = new StreamReader(filename))
            {
                textNetFile = sr.ReadToEnd();
            }
        }
        catch (Exception exp)
        {
            MessageBox.Show(exp.Message);
        }

        //выводим название выбранного файла на экран
        string[] sp = filename.Split('\\');
        labelNetFileName.Content = sp[sp.Length - 1];

        /*
         * Есть два вида NET файла.
         * В одном из них есть мета-теги $NETS, $END и т.п. 
         * Это означает, что в NET файле есть много других символов, которые не нужны.
         * От них нужно избавиться.
         * То есть находим $NETS, и если есть, то удаляем лишнее
         */

        //если NET файл содержит $NETS
        if (textNetFile.Contains("$NETS"))
        {
            int indexNets = 0;

            //находим $NETS и запоминаем его позицию
            foreach (Match m in Regex.Matches(textNetFile, @"[$]NETS"))
            {
                indexNets = m.Index;
            }

            //удаляем подстроку до $NETS (сам $NETS имеет 5 символов + удаление перевода строки)
            textNetFile = textNetFile.Remove(0, indexNets + 7);

            //и удаляем в конце $END
            textNetFile = textNetFile.Replace("\n$END", "");

            //удаляем все ;
            textNetFile = textNetFile.Replace(";", "");

            //удаляем перенос строки в одной цепи
            textNetFile = Regex.Replace(textNetFile, @",\r?\n", "");

            //разделяем по переносу строки
            split = textNetFile.Split('\n').ToList();
        }
        else
        {
            //удаляем перенос строки в самом конце (
            textNetFile = textNetFile.Substring(0, textNetFile.Length - 4);
            //разделяем по ;
            split = textNetFile.Split(';').ToList();
        }
    }
    return split;
}
```

<p align="justify">
В классе Schema храним названия цепей, элементов и номера пинов. С помощью  регулярных выражений вытаскиваем каждое найденное вхождение. Каждый NET-файл имеет однообразную тенденцию: в каждой строке
<ul>
  <li>первое вхождение - название цепи;</li>
  <li>нечетное вхождение - название элемента;</li>
  <li>четное вхождение - номер пина элемента.</li>
</ul>
</p>

<p>
Таким образом можно извлечь необходимые данные для построения матрицы цепей.
</p>

```
//создаем объект
Schema sh = new Schema();

for (int i = 0, count; i < split.Count; i++)
{
    count = 0;
    //обрабатываем каждое найденное вхождение с помощью регулярного выражения 
    //"\w+" -любой символ кроме специфических (-,)+ и т.п.)
    foreach (Match m in Regex.Matches(split[i], @"\w+"))
    {
        //если первый элемент в подмассиве, то это название цепи
        if (count == 0)
        {
            sh.СhainName = sh.setChainName(m.Value);
        }
        //если нечетный элемент в подмассиве, то это название элемента
        else if (count % 2 != 0)
        {
            sh.ElemName = sh.setElemName(m.Value);
        }
        //если четный элемент в подмассиве, то это номер пина
        else if (count % 2 == 0)
        {
            sh.ElemPin = sh.setElemPin(m.Value);
        }
        count++;
    }
}
```

<p align="justify">
Теперь необходимо сформировать размерность матрицы, для того, что вывести ее в dataGrid (не путать с datdGridView). В sh.ElemName и sh.ElemPin как раз и хранятся названия элементов и пинов. Но загвоздка заключается в том, что в sh.ElemName и sh.ElemPin имеются повторяющиеся названия элементов и номера пинов. Так избавимся от них. И отсоритруем.
</p>

```
//удаляем повторяющиеся названия элементов
sh.ElemName = sh.ElemName.Distinct();
//сортируем
sh.ElemName = sh.ElemName.OrderBy(s => s);

//удаляем повторяющиеся номера пинов
sh.ElemPin = sh.ElemPin.Distinct();
//сортируем
sh.ElemPin = sh.ElemPin.OrderBy(s => s);
```

<p>
И формируем dataGrid:
</p>

```
//заполняем DataGrid матрицей цепей
public void setDataGridMatrixChain(IEnumerable<string> elemName, IEnumerable<int> elemPin, string[][] stepArray)
{
    DataTable dt = new DataTable();

    //формируем размерность DataGrid
    dt.Columns.Add("#");

    //для файла allegro_5.NET раскоментировать эти строки
    /*
     * dt.Columns.Add("");
     * dt.Columns.Add("");
     */

    foreach (var itemPin in elemPin)
    {
        dt.Columns.Add(itemPin.ToString());
    } 

    foreach (var itemElem in elemName)
    {
        dt.Rows.Add(itemElem);
    }

    //заполняем DataGrid
    string tempChain = "";
    int tempCount = 0;

    foreach (var itemElem in elemName)
    {
        for (int i = 0, count = 0; i < stepArray.Length; i++)
        {
            count = 0;
            tempChain = stepArray[i][0].ToString();

            for (int k = 0; k < stepArray[i].Length; k++)
            {
                if (count != 0)
                {
                    if (count % 2 != 0)
                    {
                        if (itemElem.ToString() == stepArray[i][k])
                        {
                            //вместо названий цепей вставляем их "номер"
                            dt.Rows[tempCount][Convert.ToInt32(stepArray[i][k + 1])] = (i + 1);
                        }
                    }
                }
                count++;
            }
        }
        tempCount++;
    }
    dataGrid.ItemsSource = dt.DefaultView;
}
```
