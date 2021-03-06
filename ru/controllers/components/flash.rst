Флэш
####

.. php:namespace:: Cake\Controller\Component

.. php:class:: FlashComponent(ComponentCollection $collection, array $config = [])

``FlashComponent`` предоставляет способ установки одноразовых уведомлений,
которые будут отображаться после обработки формы или подтверждения данных.
CakePHP ссылается на эти сообщения как «флэш-сообщения». ``FlashComponent``
записывает флэш-сообщения в ``$_SESSION`` для дальнейшего вывода в Виде
с помощью хелпера :doc:`FlashHelper </views/helpers/flash>`.

Настройка флэш-сообщений
========================

``FlashComponent`` предоставляет два способа установки флеш-сообщений: его
магический метод ``__call()`` и его метод ``set()``. Чтобы предотвратить
многословность в приложении, магический метод ``__call()`` компонента
``FlashComponent`` позволяет использовать имя метода, которое сопоставляется
с элементом, расположенным в каталоге **templates/element/flash**.
В соответствии соглашениями, имена методов в верблюжьем регистре будут
соотносится с именем элемента, написанном в нижнем регистре с разделением слов
с помощью знака подчеркивания::

    // Использует templates/element/flash/success.php
    $this->Flash->success('This was successful');

    // Использует templates/element/flash/great_success.php
    $this->Flash->greatSuccess('This was greatly successful');

В качестве альтернативы вы можете задавать текстовое сообщение без использования
заготовленного элемента вида, используя метод ``set()``::

    $this->Flash->set('Это текст сообщения');

Методы ``__call()`` и ``set()`` компонента ``FlashComponent`` опционально
могут принимать второй параметр - массив опций:

* ``key`` По умолчанию 'flash'. Ключ массива, который можно найти в ключе
  ``Flash`` в сессии.
* ``element`` По умолчанию ``null``, но будет автоматически назначен при
  использовании магического метода ``__call()``. Передает имя элемента для
  вывода.
* ``params`` Опциональный массив ключей/значений для задания переменных,
  к которым можно обращаться из элемента.

Пример использования эти опций::

    // В вашем Контроллере
    $this->Flash->success('Пользователь сохранен', [
        'key' => 'positive',
        'params' => [
            'name' => $user->name,
            'email' => $user->email
        ]
    ]);

    // В вашем Виде
    <?= $this->Flash->render('positive') ?>

    <!-- В templates/element/flash/success.php -->
    <div id="flash-<?= h($key) ?>" class="message-info success">
        <?= h($message) ?>: <?= h($params['name']) ?>, <?= h($params['email']) ?>.
    </div>

Обратите внимание, что параметр ``element`` будет всегда переопределен при
использовании метода ``__call()``. Чтобы получить определенный элемент из плагина,
вы должны установить значение параметра ``plugin``. Например::

    // В вашем Контроллере
    $this->Flash->warning('Мое сообщение', ['plugin' => 'ИмяПлагина']);

Приведенный выше код будет использовать элемент **warning.php** находящийся в
**plugins/PluginName/templates/element/flash** для вывода флэш-сообщения.

.. note::

    По умолчанию CakePHP экранирует содержимое флэш-сообщений для предотвращения
    межсайтового скриптинга. Пользовательские данные в ваших флэш-сообщениях
    будут представлены в виде HTML-сущностей и будут  безопасны для печати.
    Если вы хотите включить HTML-разметку в ваше флэш-соощение, вам нужно
    передать параметр ``escape`` и настроить шаблоны флэш-сообщений, чтобы
    отключить экранирование при передаче значения данной опции.

    By default, CakePHP escapes the content in flash messages to prevent cross
    site scripting. User data in your flash messages will be HTML encoded and
    safe to be printed. If you want to include HTML in your flash messages, you
    need to pass the ``escape`` option and adjust your flash message templates
    to allow disabling escaping when the escape option is passed.

HTML во флэш-сообщениях
=======================

Возможен вывод HTML во флэш-сообщениях с помощью опции ``'escape'``::

    $this->Flash->info(sprintf('<b>%s</b> %s', h($highlight), h($message)), ['escape' => false]);

Убедитесь в том, что вы вручную экранируете ввод в подобном случае. В примере
выше ``$highlight`` и ``$message`` не являются HTML-разметкой и благодаря
этому экранируются.

Для более подробной информации о выводе ваших флэш-сообщений, пожалуйста
просмотрите раздел :doc:`FlashHelper </views/helpers/flash>`.
