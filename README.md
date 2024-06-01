DEMKA

### **Первый этап** настройка Deb1,2,3:
    
    su

    nano /etc/network/interfaces
в файле nano /etc/network/interfaces надо написать(то что в <?> может отличатся):

    allow-hotplug enp0s3

    iface enpos3 inet static

    address <ip>

    netmask <mas>

    up ip route add 5.6.7.0/26 via 8.9.10.1
    
    up ip route add 2.3.4.0/27 via 8.9.10.3
    
затем сохроняем ctrl+o, и выходим ctrl+x

Перезагрузка затем ниже

    nano /etc/sysctl.conf (убрать # с net.ipv4.ip_forward=1)

затем прописывается маршрут:

    ip route add 5.6.7.0/26 via 8.9.10.30

    ip route add 2.3.4.0/27 via 8.9.10.20

перезагружаем, проверяем айпишники с помощью ip a, если всё сохронилось то можно приступать к настройки ip на сервере, потом проверить пинг и после успешной проверки дальнейшей установке служб пункт 2.

### **Второй этап** установка и настройка ADDS, DNS, DHCP, создание пользователей и ввод их в домен.

Полезные сылки: https://softcomputers.org/blog/nastroika-windows-server-2019/

https://habr.com/ru/companies/testo_lang/articles/525326/

### **Третий этап** установка ЦС. 

Полезные сылки: https://abuzov.com/active-directory-certificate-services/

https://dev.rutoken.ru/pages/viewpage.action?pageId=57148969


Перед началом выполнения отключили параметр Конфигурация усиленной безопасности Internet Explorer на вкладке Локальный сервер. Далее, в открывшемся окне отметили значения Отключить.

Перешли в Диспетчер серверов -> Добавление ролей и компонентов. В мастере, в разделе Роли сервера нашли и отметили роль Службы сертификатов Active Directory, а также IIS веб-сервер.
![Screenshot](https://github.com/NatyaCool/demka/blob/main/1.png)
В разделе Службы ролей убедились, что пункт Центр сертификации отмечен для установки.

Подтвердили все параметры и запустили установку службы. По окончанию установки появилась активная строка «Настроить службы сертификатов Active Directory на конечном сервере», нажали по строке и перешли к настройке.

Первоначально проверили, что Учетные данные введены, после нажали Далее.

Затем в разделе Службы ролей, установили галочки рядом с параметром Центр сертификации и Служба регистрации в центре сертификации через Интернет.

После, выбрали ЦС предприятия, так как Наш ЦС находится внутри доменной сети.

Далее указали, что это главный центр сертификации, т.е. Корневой ЦС.

После, необходимо создать новый закрытый ключ.

Последующим этапом конфигурации указали поставщика служб шифрования, длину ключа и хэш-алгоритм, применяемый для шифрования.

После, конфигуратор требует указать имя ЦС. Все значения оставили по умолчанию.

Далее, указали срок действия сертификата для ЦС.

Дальше, в конфигураторе указали расположение баз данных их значения мы оставили по умолчанию.
 
В разделе Подтверждение проверили, что все указано верно и кликнули Настроить.
 
В результате мы увидели уведомление о успешном выполнении настройки.
 



### **Четвертый этап** 

Создать пользователей в подразделении Krypto. Открываем на сервере «Active Directory – пользователи и компьютеры» и создаем нового пользователя. Задаем имя пользователю домена. Задаем пароль и снимаем галочки, если они есть.

Открываем «Центр сертификации» на сервере.
Рисунок 4 – центр сертификации
ПКМ на «шаблоны сертификатов» - «управление».
Рисунок 5 – выбор пункта «управление»
Копируем шаблон «Пользователь со смарт-картой»
Рисунок 6 – пользователь со смарт-картой
Открывается окно со свойствами нового шаблона. В «Общие» задаем
имя шаблона и периоды. Нажимаем «применить».
Рисунок 7 – вкладка «Общие»
В «Шифрование» указываем поставщика для запросов – «Microsoft Base
Smart Card Crypto Provider». Нажимаем «применить».
Рисунок 8 – вкладка «Шифрование»
В «Безопасность» выдаем разрешения для группы «Прошедшие
проверку» на «Заявку» и «Автоматическую подачу заявок». Нажимаем
«применить».
Рисунок 9 – вкладка «Безопасность»
В «Требования выдачи» указываем тип политики «Политика
применения» и в политике применения «Агент запроса сертификата».
Нажимаем «применить». Остальные свойства были оставлены по умолчанию.
Рисунок 10 – вкладка «Требования выдачи»
После этого в консоли находим шаблон «Агент регистрации» и жмем
ПКМ – «Свойства».
Рисунок 11 – агент регистрации
Во вкладке «Безопасность» выдаем право на «Заявку» и «Чтение» для
группы «Прошедшие проверку». Нажимаем «применить».
Рисунок 12 – вкладка «Безопасность» агента регистрации
Возвращаемся в Центр сертификации и жмем ПКМ – «Создать» -
«Выдаваемый шаблон сертификата».
Рисунок 13 – выдаваемый шаблон сертификата
Выбираем ранее созданный шаблон для пользователя.
Рисунок 14 – ранее созданный шаблон
Создаем еще один выдаваемый шаблон сертификата – «Агент
регистрации».
Рисунок 15 – агент регистрации
По итогу окно «Центра сертификации» должно выглядеть следующим
образом.
Рисунок 16 – итог промежуточной работы
Заходим в проводник на сервере и идем по пути
C:\Windows\System32\CertSrv\CertEnroll и находим там файл сертификата
сервера.
Рисунок 17 – сертификат сервера
ПКМ по файлу сертификата – «Установить сертификат».
Рисунок 18 – установка сертификата
Откроется мастер импорта сертификатов. Выбираем «Локальный компьютер».
Рисунок 19 – мастер импорта
Выбираем «Поместить все сертификаты в следующее хранилище» -
«Обзор» - «Доверенные корневые центры сертификации».
Рисунок 20 – выбор хранилища
Жмем на следующем этапе «Готово» и видим сообщение об успешном
импорте.
Рисунок 21 – успешный импорт
Заходим на клиенте за ранее созданного пользователя.
Рисунок 22 – вход за созданного пользователя
Далее стоит задача скопировать сертификат сервера с самого сервера на
клиент. Это можно сделать любым удобным способом (сетевая папка, SFTP и
т.д.). В данной же работе передача сертификата была выполнена при помощи
перетаскивания файла с ВМ на хост, и наоборот с функцией Drag’n’drop.
Рисунок 23 – передача сертификата
Перетаскивание файла сертификата с хоста на ВМ клиента (если
перетаскивание не работает – попробуйте Ctrl+C, Ctrl+V).
Рисунок 24 – перетаскивание файла
Устанавливаем сертификат, также, как и на сервере (шаги 18 – 21).
В ходе установки сертификата на локальный компьютер потребуют учетные
данные администратора, используем «Администратор» и пароль от
администратора домена.
Рисунок 25 – установка сертификата
Открываем на клиенте «Управление сертификатами пользователей».
Рисунок 26 – управление сертификатами пользователей
Нажимаем на папку «Личное», затем ПКМ по пустой области, «Все
задачи» - «Запросить новый сертификат».
Рисунок 27 – запрос нового сертификата
Нажимаем «Далее», «Далее» и видим список доступных для запроса
сертификатов. В первую очередь запрашиваем «Агента регистрации»,
нажимаем «Заявка».
Рисунок 28 – агент регистрации
Результатом установки сертификатов должно быть следующее сообщение.
Рисунок 29 – результат установки
Появляется папка «Сертификаты», а в ней новый сертификат.
Повторяем процедуру с запросом.
Рисунок 30 – папка «Сертификаты»
Нажимаем на синее сообщение под нашим созданным шаблоном
«Пользователь familia.i.o.»
Рисунок 31 – данные для подачи заявки
Нажимаем на «Обзор» рядом с «Сертификатом подписи».
Рисунок 32 – кнопка «Обзор»
Появляется окно о подтверждении сертификата, жмем «ОК».
Рисунок 33 – окно подтверждения сертификата
После этого жмем «Применить» и «ОК».
Рисунок 34 – подтверждение
Теперь сообщения нет и можно делать заявку на сертификат созданного
шаблона.
Рисунок 35 – заявка на сертификат
Появляется сообщение о подключении «Смарт-карты», жмем «Отмена» и
радуемся жизни
Рисунок 36 – подключение по смарт-карте
Возвращаемся на сервер, жмем «Локальный сервер» и включаем
удаленный рабочий стол.
Рисунок 37 – локальный сервер
Открываем «Подключение к рабочему столу» на клиенте и в качестве
компьютера пишем полное доменное имя сервера, а также пользователя
«Администратор».
Рисунок 38 – подключение по RDP
После установки соединения в верхней панели появится значок замочка,
нажав на который можно убедиться в защищенности соединения. В случае
если его нет, попробуйте установить сертификат.
Рисунок 39 – успешное подключение


### **Пятый этап OpenSSL** 

### **Шестой этап MySQL** 

### **Седьмой этап WireGuard** 
