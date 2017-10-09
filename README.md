# HTML.CacheLib
Библиотека
Скрипт(JS):
<script type="text/javascript" src='HTML.CacheLib.cls'></script>

Описание: 
 1) Реализована трансляция Cache` классов в JS функции глобальные. 
 2) Реализована трансляция Cache` классов в прототипы JS при инициализации страницы и в коде 
 3) Реализована функция динамического подключения библиотек (js и css - файлы) 
 4) Реализована функция динамического подключения прототипов и глобальных функция. 
 5) Реализована возможность запуска класс методов с передачей в функцией (callback) обработки результата в качестве аргумента.

Динамическое подключение библиотек в JS коде:
    а) Подключаем базовую библиотеку на  CSP странице:
         <script src='HTML.CacheLib.cls' type='text/javascript' ></script>
    б) Подключение библиотек JS,CSS,cls:

	 
	<script language="JavaScript">
	   // Подключаем библиотеку JS из класса
	   includeClass('HTML.js.WebGL.BabylonJS.babylon.cls','javascript');
	   // Подключаем JS библиотеку из файла
	   includeFile('https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js','javascript');
	   // Подключаем JS библиотеку из файла
	   includeFile('https://jQuery.min.js');
	   // Подключаем CSS библиотеку из файла
	   includeFile('https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css','css');
	  </script>


Примечание : Функции расположенные внутри, подключаемых классов, станут доступны после полной инициализации страницы.


Применение функции runMethod :

 а) Создаем классметод:
 	  Class User.test
	  {
	    ClassMethod Test1 (arg) As %String
	    {
	         w "Test Message:" _arg
	         q ""
	     }
	  }


    б) Подключаем базовую библиотеку на  CSP странице:
         <script src='HTML.CacheLib.cls' type='text/javascript' ></script>
    в) Синхронный вызов класс метода:

	 
	                <script language="JavaScript">
	                      alert ( runMethod('User.test.Test1', 'arg1233') );
	                </script>

г) Асинхронный вызов класс метода:

	 
	 <script language="JavaScript">
	    /// функция обработки ответа
	    resultFun=function(txt){    alert(txt);       }
	    runMethod('User.test.Test1',resultFun,'arg1233');
	 </script>

д) Вызов класс метода Test2 расположенного на странице CSP:

	 
	  <script language="JavaScript">
	    /// функция обработки ответа
	    resultFun=function(txt){    alert(txt);       }
	    runMethod('Test2',resultFun,'arg1233');
	  </script>

Расположение глобальной JS функции в Cache` классе: а) Создаем класс:

	 
	  Class User.test  Extends HTML.CacheLib
	  {
	     ClassMethod Test1(fff,ddd) [ Language = cache, WebMethod ]
	     {
	        w fff_"---"_ddd
	     }
	     ClassMethod Test2(Arg1,Arg2) As %String [ Language = cache, WebMethod ]
	     {
	        q Arg1_"----Вар2----"_Arg2
	     }
	     ClientMethod Test3(Arg1) As %String [ Language = javascript, WebMethod ]
	     {
	        alert(Arg1);
	     }
	  }


    б) Подключаем базовую библиотеку на  CSP странице:
         
    в) Синхронный вызов класс метода:
                
    г) Асинхронный вызов класс метода:

	 
	 <script language="JavaScript">
	    /// функция обработки ответа
	    resultFun=function(txt){ alert(txt); }
	    Test1(resultFun,'arg1233');
	 </script>

Примечание : в ClassMethod и ClientMethod необходимо указывать  параметр WebMethod  и язык написания кода Language = javascript .
Создание JS прототипа в Cache` классе
         а) Создаем класс:

	 
	 Class User.Prototyp Extends (HTML.CacheLib, User.Prototype2, User.NewClass2)
	 {
	  /// Альтернативное название прототипа.
	  /// Если этого параметра нет, тогда имя прототипа
	  /// берется из имени класса “Prototyp”
	  Parameter ClassName = "ClassName";
	  /// Публичные переменные прототипа
	  Property Pole1 As %String [ InitialExpression = "Value1", Internal ];
	  Property Pole2 As %String [ InitialExpression = "Value2", Internal ];
	  Property Pole3 As %String [ Internal ];
	  /// Конструктор прототипа
	  ClientMethod Prototyp(arg1 = "") As %String [ Internal, Language = javascript ]
	  {
	     this.Pole1=arg1;
	  }
	  ClassMethod Test() As %String [ Internal, Language = cache ]
	  {
	     zw
	     q ""
	  }
	  ClientMethod Test3(arg1 = "") As %String [ Internal, Language = javascript ]
	  {
	    alert(arg1);
	  }
	 }


    б) Подключаем базовую библиотеку на  CSP странице:
         
    в)  Применение прототипа:

	 
	   <script  language="JavaScript">
	     var edit=new Prototyp('Test-Prototyp');// Создаем экземпляр прототипа
	     alert(edit.Pole1); // обращение к переменной
	     alert(edit.Test());// синхронный вызов функции
	     retunFun=function(txt){ alert(txt); }
	     edit.Test(retunFun); // асинхронный вызов
	     edit.Test3(retunFun,'Args123')// асинхронный вызов
	  </script>

Примечание :
1)       При наследовании прототипов все свойства и методы родителей, так же наследуются.
2)       Для включения элемента в состав прототипа необходимо указать параметр “ Internal” и название языка написания кода “Language = javascript”.
3)       Из класс метода Cache невозможно напрямую обращаться к полям прототипа. В ClientMethod  поля прототипа доступны по JS правилам.
4)       Если в прототипе описана глобальная функция (с параметром WebMethod  ) она будет интерпретирована как глобальная функция
Пример создания глобальной переменной JS в классе Cache
Добавляем в параметр Property свойство "[ServerOnly = 1]" при интерпритации класса это поле превратится в глобальную JS переменную.

	 
	 Class Demo.Three.Panarama2 Extends %CSP.Page
	 {
	     Property GlobalVar1 As %String [ InitialExpression = {$h}, ServerOnly = 1 ];
	     Property GlobalVar2 As %String [ InitialExpression = "Тестовый текст инициируется при загрузке страницы", ServerOnly = 1 ];
	     ClientMethod MyAlert(arg) [ Language = javascript, WebMethod ]
	     {
	       alert(arg);
	     }
	     ClassMethod OnPage() As %Status
	     {
	        &html<
	          <script src='HTML.CacheLib.cls' type='text/javascript' ></script> 
	          <script type='text/javascript' >
	             alert(GlobalVar1);
	             MyAlert(GlobalVar2);
	          </script> 
	        >
	        q $$$OK
	     }
	 }


Пример вызова Cache сода из JS клиент метода
Добавляем в параметр Property свойство "[ServerOnly = 1]" при интерпритации класса это поле превратится в глобальную JS переменную.

	 
	  Class Demo.Delete Extends %CSP.Page
	  {
	          ClientMethod MyAlert(arg) [ Language = javascript, WebMethod ]
	          {
	              //  /*(  'Команда cache'  )*/
	              // Выполнить однострочную команду 
	              alert(' /*( w $zdt($h))*/ '+arg)
	          }
	          ClassMethod OnPage() As %Status
	          {
	              &html<
	                  <script src='HTML.CacheLib.cls' type='text/javascript' ></script> 
	                   <script language='javascript'>
	                      MyAlert('Тестовое сообщение');
	                 </script>
	              >
	              Quit $$$OK
	          }
	    }
	 

Создание собственного HTML элемента в Cache классе (работа с прототипами).
Создаем прототип HTML элемента:

	  Class User.Prototyp2 Extends HTML.CacheLib
	  {
	  /// Название класса для создания элемента из JS
	  Parameter ClassName = "TestHtmlTag";
	  /// имя тэга <my-input>0</my-input>
	  Parameter tagName = "my-input";
	  Property timer As %Integer [ InitialExpression = 0, Internal ];
	  /// содержимое Тэга
	  Property innerHTML As %String [ InitialExpression = "Value1", Internal ];
	  /// JS Конструктор HTML элемента
	  ClientMethod prototyp2(arg1 = "") As %String [ Internal, Language = javascript ]
	  {
	    this.Pole1=arg1;
	  }
	  /// запуск таймера
	  ClientMethod attachedCallback() As %String [ Internal, Language = javascript ]
	  {
	     setInterval(this.tick.bind(this), 1000);
	  }
	  /// Функция срабатывает после создания элемента (инициализация)
	  ClientMethod createdCallback() As %String [ Internal, Language = javascript ]
	  {
	     this.timer = 0;
	  }
	  /// функция инкремента
	  ClientMethod tick() As %String [ Internal, Language = javascript ]
	  {
	      this.timer++;
	      this.innerHTML = this.timer;
	  }
	  
	  }
	  
	 

Создание HTML элемента в CSP файле

	  <html>
	  <head>
	  <title> Cache Server Page </title>
	  </head>
	  <body>
	    <script src='HTML.CacheLib.cls' type='text/javascript' ></script>
	    <script src='User.Prototyp2.cls' type='text/javascript' ></script>
	    <my-input>222222</my-input>
	  </body>
	  </html>
	 

Подключение JS библиотек из классов

	 /// Подключаемый класс
	 Class User.Test
	 {
	    ClientMethod MyAlert(txt) [ Language = javascript, WebMethod ]
	    {
	       alert(txt);
	    }
	 
	 }
	   /// Применение  <include-js src="User.Test"></include-js>
	   Class User.includeTest Extends %CSP.Page
	   {
	   
	   ClassMethod OnPage() As %Status
	   {
	     &html<
	       <script src='HTML.CacheLib.cls' type='text/javascript' ></script>       
	       <include-js src="User.Test"></include-js>
	       <include-css src='User.Test'></include-css>
	     >
	    &html<
	       <script type='text/javascript' >
	         MyAlert('ssssssssssss');
	      </script>
	     >
	      Quit $$$OK
	   }
	   
	   }
	 

