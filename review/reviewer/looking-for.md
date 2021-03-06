# На что обращать внимание в ревью

Замечание: Всегда учитывайте [Стандарты код-ревью](standard.md) при рассмотрении каждого из принципов.


## Дизайн (структура)

Самое важное, на что стоит обратить внимание в ревью - это дизайн CL в целом. Как взаимодействует код в рамках CL? Где находятся изменения: в общей кодовой базе или в вынесены в отдельную библиотеку? Насколько хорошо они интегрируются с остальными компонентами системы? Подходящее ли сейчас время для данных изменений?


## Функциональность

Делает ли CL то, для чего он задуман? Насколько хорошо продуманы изменения для пользователей? При этом под "пользователем" понимается как конечный пользователь (если его затрагивают изменения), так и разработчики, которые будут использовать код в дальнейшем.

Предполагается, что разработчики достаточно хорошо тестируют свой код и на момент передачи в ревью он работает корректно. Вместе с тем, с точки зрения ревьюера, вы должны думать о граничных случаях, проблемах конкурентности, представлять себя на месте конечного пользователя, а также не забывать о багах, которые можно обнаружить просто читая код.

Существуют ситуации, когда может потребоваться дополнительная проверка CL: например, когда изменения касаются пользовательского интерфейса и через чтение кода сложно представить как это затронет конечного пользователя. В подобных ситуациях вы можете попросить разработчика сделать небольшое демо с новым функционалом, если запустить этот код самому представляется затруднительным.

Еще один случай, когда стоит отнестись к ревью более пристально - это наличие в коде CL параллельности в том или ином виде. Главные проблемы, которые может привнести параллельность - это дедлоки и гонки. Эти проблемы бывают очень трудноуловимыми, поэтому, необходимо чтобы и ревьюер и разработчик отнеслись к данному коду внимательнее. (Сложность ревью также является веской причиной избегать моделей конкурентности с возможными гонками и дедлоками).


## Сложность

Проверяйте сложность кода на всех уровнях CL - в отдельных строках, функциях, классах. Слишком высокая сложность означает, что во-первых, нельзя быстро понять как работает код, во-вторых, при внесении изменений наверняка появятся баги.

Обычный случай, в результате чего код получается слишком сложным, это когда разработчики пишут слишком общий код или добавляют функционал, который сейчас не нужен в системе. Ревьюеры должны быть очень бдительны на предмет **оверинжиниринга** и должны убеждать разработчиков решать только те задачи, которые должны быть решены сейчас, и не касаться того, что, может быть и не придется делать в дальнейшем. Будущие проблемы должны решаться по мере поступления, в тот момент когда они принимают конкретные черты и получают ясные требования.


## Тесты

Код, проходящий ревью, должен быть покрыт тестами: требуйте юнит, интеграционных или end-to-end тестов при проведении ревью. В общем случае, тесты должны быть добавлены в тот же CL, что и код. Исключениями являются
[экстренные случаи](../emergencies.md).

Проверяйте правильность тестов - они не тестируют сами себя, а тесты на тесты пишутся крайне редко, обычно их проверяет человек.
Упадут ли тесты, если сломается код? Будут ли они работать правильно, если код изменится? Все ли тесты простые и проверяют то, что нужно проверять? Корректно ли разбиты проверки по методам?

Помните, что тесты - это тоже код, который нужно поддерживать. Не позволяйте тестам быть слишком сложными, просто потому, что они не входят в конечный релизный файл.


## Наименования

Необходимо проверять, что разработчик выбрал подходящие наименования. Хорошее имя должно быть достаточно полным, чтобы понять, за что отвечает компонент, но вместе с тем, оставаться легко читаемым и не быть слишком длинным.


## Комментарии

Насколько понятны и необходимы комментарии, оставленные разработчиком? Написаны ли они на понятном английском? Комментарии полезны, когда объясняют почему данный код существует, но излишни, если рассказывают о том, что делает код, хотя бывают и исключения: например, регулярные выражения и сложные алгоритмы. Если код сам по себе не является наглядным, то необходимо сделать его проще. Однако, чаще всего, комментарии должны пояснять то, чего не может сказать код: те решения, которые стоят за написанным.

Бывает полезным посмотреть комментарии, оставленные до CL: возможно, там есть "TODO", которое теперь можно убрать или совет про то, как должен быть сделан некоторый функционал.

Важно заметить, что комментарии - это не тоже самое что **документация** классов, модулей и функций. Документация нужна как раз для того, чтобы описать что делает код и как его использовать.


## Стиль

В Google есть [стайл гайды](http://google.github.io/styleguide/) для всех основных и большинства вспомогательных языков программирования, которые мы используем. Убедитесь, что CL соответствует требованиям в соответствующих гайдах.

Если вы делаете замечание, которое касается стиля и этот момент никак не  освещается в гайде, то помечайте такое требование как необязательное ("Nit:"). CL не должен блокироваться на основании личных стилистических предпочтений.

Разработчик не должен мешать в один CL стилистические правки и исправление функционала. Это делает сложным для понимания, какой именно функционал изменился. Например, если разработчик хочет полностью переписать целый файл, поменяв его функционал и зарефакторив, то требуйте от него двух CL - первый с рефакторингом, а второй уже с функциональными изменениями.


## Документация

Если CL меняет процесс взаимодействия пользователей с кодом, например, то, как теперь работают тесты или проходят релизы, следует также проверять соответствующие изменения в документации, включая README, g3doc (прим. - внутренний инструмент Google для хранения документации) и любые ссылки на сгенерированные документы. Если CL удаляет код, проверьте, что соответствующий раздел в документации также удален. Если документации нет, то запросите ее.


## Каждая строчка {#every_line}

Проверяйте *каждую* строчку кода. Некоторые данные, такие как сгенерированный код или гигантская структура данных, можно читать по диагонали, но никогда не пролистывайте просто так код, написанный человеком. Конечно, что-то должно быть изучено пристальнее - вы должны сами провести для себя грань что именно и насколько глубоко. Помните, что вы всегда должны понимать что делает код.

Если вам сложно понять что происходит и ревью затягивается, то дайте знать об этом  разработчику и, прежде чем продолжать ревью, дождитесь пояснений. В Google работают очень сильные разработчики и вы один из них. Если вы не понимаете, что написано, то, с большой долей вероятности, не поймут и другие. Таким образом, запрашивая пояснения, вы помогаете не только себе, но и вашим коллегам, которые столкнуться с этим кодом в дальнейшем.

Если вы понимаете что делает код, но не чувствуете себя достаточно компетентным для проведения ревью, убедитесь, что среди ревьюеров есть человек с соответствующей квалификацией по данному вопросу. Стоит особенно принципиально относится к этой проблеме, когда дело касается безопасности, доступности, многопоточности, локализации и тд.


## Контекст

Обычно, инструменты для проведения ревью показывают только то, что поменял разработчик и небольшие фрагменты кода около изменений. Но, часто, необходимо полностью просмотреть целый файл чтобы понять контекст: предположим, вы видите, что были добавлены 4 строчки, однако, развернув файл вы понимаете что это строчки в методе из 50 строк и его теперь необходимо рефакторить.

Всегда полезно думать о CL в контексте системы в целом: улучшает ли данный CL код проекта или делает его менее понятным, хуже протестированным и тд?
Не принимайте CL, которые ухудшают состояние кодовой базы. Часто, проекты становятся слишком сложными именно по мере внесения в них мелких изменений, поэтому важно следить за сложностью в каждом отдельно взятом CL.


## Позитивные моменты {#good_things}

Если вы видите в CL хорошо сделанную работу, то скажите об этом разработчику: ревьюеры должны не только обращать внимание на ошибки, но и поощрять за качественно написанный код. С точки зрения менторства намного более важно говорить о том, что сделано хорошо, чем заострять внимание на плохих моментах.


## Итоги

Самое важное при проведении ревью:
- Код хорошо спроектирован
- Функционал работает правильно
- Изменения в пользовательском интерфейсе требуют особого внимания: помимо чистоты кода необходимо убедиться в том, что для пользователя изменения выглядят как задумано,
- Параллельно выполняемый код исполняется безопасно,
- Код не переусложнен,
- Нет надуманного и ненужного на данный момент функционала,
- На код написаны тесты
- Тесты хорошо спроектированы,
- Использованы корректные имена для файлов, классов, методов, переменных и тд
- Оставлены понятные и нужные комментарии, по большей части поясняющие причины решений, а не суть (суть должна быть понятна из самого кода),
- Код задокументирован (g3doc),
- Код соответствует нашим стайл-гайдам.

Вы должны просмотреть **каждую строчку** кода, брать во внимание **контекст**, быть уверенным в том, что **улучшаете состояние кодовой базы** и поощрять удачные решения разработчика.

Далее: [Навигация в по списку изменений в ревью](navigate.md)
