wb-mqtt-gpio драйвер
====================

Драйвер wb-mqtt-gpio получает необходимые параметры для запуска с файла /etc/wb-mqtt-gpio.conf, в этом файле должны находиться
описания GPIO, с которыми драйвер должен работать. Примеры конфигурационных файлов находятся в папке /usr/share/wb-mqtt-gpio/.

Конфигурационный файл:

``` jsonc
{
    // отображаемое имя устройства. Публикуется как
    // .../meta/name в MQTT
    "device_name" : "GPIO DEVICES",

    "channels" : [
    ]
}
```

Описание одного канала соответствует описанию отдельного GPIO. Параметры, которые описывают GPIO, ниже в примерах.
Рекомендуется использовать новый интерфейс работы с GPIO, появившийся в ядре 4.8. 
Подробнее можно прочитать по ссылке https://ostconf.com/system/attachments/files/000/001/532/original/Linux_Piter_2018_-_New_GPIO_interface_for_linux_userspace.pdf

Формат настройки GPIO для нового интерфейса:

``` jsonc
    "gpio" : {
        // путь к чипу GPIO
        "chip": "/dev/gpiochip0",

        // сдвиг линии относительно чипа
        "offset" : 22,
    },
```

Пример полного конфигурационного файла:

``` jsonc
{
    "device_name" : "GPIO DEVICES",

    "channels" : [
    // канал, в котором не указан direction, по умолчанию работает на выход
        {
    // name значение которое передается в MQTT в примере ../controls/FET_1
            "name" : "FET_1",

    // чип и линия GPIO, который описывает данный канал
            "gpio" : {
                // путь к чипу GPIO
                "chip": "/dev/gpiochip0",

                // сдвиг линии относительно чипа
                "offset" : 22,
            },
    // либо номер GPIO, который описывает данный канал. Это устаревший вариант настройки. Не рекомендуется к использованию
            "gpio" : 22,
    
    // параметр inverted задает инвертированный канал или нет
            "inverted" : false
        }

    //также gpio могут работать на вход и считывать изменения напряжения с клеммника
        {
            "name" : "D1_IN",
            "gpio" : {
                "chip": "/dev/gpiochip1",
                "offset" : 36,
            },
            "inverted" : false,

    //для этого указать direction input
            "direction" : "input"

    // rising прерывания по восходящему фронту, falling по нисходящему,
    //both по обоим фронтам, для GPIO с незаданным type по умолчанию устанавливается both
    //для счетчиков определяется автоматически, если не указан.
            "edge" : "falling"
        }

    //для работы с счетчиком электроэнергии
        {
            "name" : "D3_IN",
            "gpio" : {
                "chip": "/dev/gpiochip1",
                "offset" : 38,
            },
            "direction" : "input",
            "inverted" : false,

    // type указывает что GPIO описывает счетчик электроэнергии
            "type" : "watt_meter",

    //множитель для расчета, количество импульсов на kWh
            "multiplier" : 1000
        },

    //для работы с счетчиком воды
        {
            "name" : "D3_IN",
            "gpio" : {
                "chip": "/dev/gpiochip1",
                "offset" : 38,
            },
            "direction" : "input",
            "inverted" : false,

    // type указывает что GPIO описывает счетчик воды
            "type" : "water_meter",

    //множитель для расчета, количество импульсов на м^3
            "multiplier" : 100,

    //число знаков после запятой в текущем потреблении (воды, электричества, etc)
            "decimal_points_current" : 2,

    //число знаков после запятой в полном потреблении (воды, электричества, etc)
            "decimal_points_total" : 3,


        }
    ]
}

```
