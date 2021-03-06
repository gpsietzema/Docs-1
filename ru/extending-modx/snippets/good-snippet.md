---
title: "Написание хорошего сниппета"
translation: "extending-modx/snippets/good-snippet"
---

Для некоторых написание сниппета MODX может быть их первой пробой пера в програмировании. Вот несколько советов для новичков и опытных разработчиков.

## Our Example Snippet

``` php
<?php
/**
 * mySnippet
 *
 * ОПИСАНИЕ
 *
 * Этот фрагмент кода умножает числа &x и &y - это демонстрирует
 * некоторые хорошие привычки.
 *
 * СВОЙСТВА:
 *
 * &x требуется целое число
 * &y требуется целое число
 * &z целое число необязательно. По умолчанию: 1
 *
 * ИСПОЛЬЗОВАНИЕ:
 *
 * [[!mySnippet? &x=`5` &y=`7`]]
 *
 */
$x = (int) $modx->getOption('x', $scriptProperties);
$y = (int) $modx->getOption('y', $scriptProperties);
$z = (int) $modx->getOption('z', $scriptProperties, 1);
// Для отладки:
$modx->log(modX::LOG_LEVEL_DEBUG
    , '[mySnippet] вызван на странице '. $modx->resource->id . ' со следующими свойствами: '
    .print_r($scriptProperties, true));
// Проверка Входных Данных
if (!isset($scriptProperties['x']) || !isset($scriptProperties['y'])) {
    $modx->log(modX::LOG_LEVEL_ERROR, '[mySnippet] отсутствуют необходимые свойства &X и &г!');
    return;
}
return $x * $y * $z;
?>
```

## Хорошее имя

Дайте вашему сниппету имя, которое имеет смысл для тех, кто не знаком с ним. Хорошее имя легко запомнить.

## Описание

_Всегда_ включите краткое описание, которое объясняет, что делает ваш сниппет. Должно быть достаточно ясно, что незнакомец (например, другой пользователь панели управлеения, не обязательно разработчик) может понять, что делает ваш код, просто прочитав ваше описание.

## Comment Block

_Всегда_ включите блок комментариев в свой сниппет! Это, вероятно, самая важная часть вашего сниппета! Даже если вы не являетесь программистом, вы можете определить хорошего разработчика просто по качеству их документации и комментариев.

Блок комментариев должен содержать следующие элементы:

### ОПИСАНИЕ
  
Здесь вы можете описать свой код более подробно.
  
### СВОЙСТВА
  
Подробно укажите, какие именно свойства могут быть переданы в ваш фрагмент _и_ какой тип данных принимает каждое свойство. Также следует указать, является ли свойство обязательным или имеет значение по умолчанию. Вот несколько примеров:
  
`&day` целое число число от 0 (воскресенье) до 6 (суббота), представляющее день недели. (обязательное)
`&is_secure` логическое значение 1 использует HTTPS-соединение, 0 использует подключение по протоколу HTTP. По умолчанию: 1
`&formatTpl` строковое имя блока, используемого для форматирования выходных данных. MyChunk по умолчанию

### ИСПОЛЬЗОВАНИЕ
  
_Всегда_ приведите несколько примеров использования вашего сниппета. Это должны быть примеры, которые пользователи могут буквально копировать и вставлять на свои страницы, чтобы увидеть, как работает ваш сниппет.

## Установить Свойства По Умолчанию

Задайте свойства по умолчанию на вкладке свойства сниппета. Вы можете прочитать свойства, переданные сниппету, и задать свойства по умолчанию с помощью метода **getOption**. Помните, что все фрагменты передаются массив `$scriptProperties`.

``` php
$headTpl = $modx->getOption('headTpl', $scriptProperties, 'myHeadTpl');
```

## Не включайте HTML

Ваш сниппет должен быть максимально чистым от HTML. Если вам нужно отформатировать вывод, используйте сниппет для форматирования вывода. Это важный архитектурный принцип!

``` php
$props = array(
    'cow' => 'Moo',
    'pig' => 'Oink',
);
return $modx->getChunk('myChunk', $props);
```

Используйте плейсхолдеры в чанках

``` html
<!-- myChunk -->
Корова говорит "[[+cow]]", а свинья говорит "[[+pig]]".
```

## Не используйте Print или Echo

Никогда не печатайте и не повторяйте значения в вашем фрагменте кода. Фрагменты подобны функциям: они должны _возвращать_ данные. При использовании инструкций `print` или `echo` может показаться, что они работают. Они могут иметь неожиданные результаты. Всегда собирайте выходные данные в переменную строку, например `$output`, и возвращайте эту переменную строку.

## Консистенция

**Имена переменных:** независимо от вашего стиля програмирования, будьте последовательны. Если вы хотите использовать имена переменных **camelCase**, то убедитесь, что все ваши переменные используют этот стиль.

**Отступы:** используйте один и тот же стиль отступов.

## Ошибки журнала и информация

MODX имеет функцию ведения журнала: _используйте это_. Смотреть [xPDO::log()](extending-modx/xpdo/class-reference/xpdo/xpdo.log). Если пользователи забыли включить обязательное свойство, зарегистрируйте ошибку, чтобы пользователи знали об этом.

``` php
$modx->log(modX::LOG_LEVEL_ERROR, '[mySnippet] missing the &xyz property!');
```

Вы также можете регистрировать отладочную информацию, которая полезна для пользователей, которые отлаживают вещи. Это будет записано в журнал ошибок только в том случае, если системный параметр `log_level` установлен на соответствующий уровень.

``` php
$modx->log(modX::LOG_LEVEL_DEBUG, '[mySnippet] вызван со следующими свойствами: '.print_r($scriptProperties,true));
```

## Смотрите также

1. [Создание шаблонов сниппетов](extending-modx/snippets/templating)
2. [Добавление CSS и JS на ваши страницы через сниппеты](extending-modx/snippets/register-assets)
3. [Как написать хороший Сниппет](extending-modx/snippets/good-snippet)
4. [Как написать хороший чанк](extending-modx/snippets/good-chunk)
