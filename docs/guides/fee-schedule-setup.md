# Настройка и импорт тарифных сеток (Fee Schedules)

## 1. Обзор и назначение

Тарифная сетка (**Fee Schedule**) определяет стоимость медицинских услуг при автоматизированном расчете начислений (биллинге).

### Основные правила определения стоимости:
* **Self Pay:** Начисления формируются через раздел `Setup` -> `Billing` -> `Billing Charges`.
* **Компонент 26 (Professional Component):** При создании клеймов система определяет локацию на основе ZIP-кода врача (`Reading Doctor`), сопоставляет его с локальностью (`Setup` -> `Billing` -> `Localities`) и извлекает соответствующую тарификацию из `Setup` -> `Billing` -> `Fee Schedules`.
* **Компоненты GLOBAL / TC (Technical Component):** Стоимость определяется по ZIP-коду медицинского учреждения (Facility) или места проживания пациента (если `PoS = 12`).
* **Facility Billing:** При создании инвойсов параметры поля `Charge based on` ссылаются на локальность (`Locality`), а не на страховую компанию.

> **Системная конфигурация:**  
> Режим работы с Fee Schedules активируется системной переменной:  
> ```ini
> billing.fee_schedule.enabled = true
> ```

---

## 2. Пошаговый процесс настройки и импорта

### Шаг 1. Создание и конфигурация локальностей (Localities)

1. Перейдите в интерфейсе: `Setup` -> `Billing` -> `Localities`.
2. Добавьте новую запись локальности для целевого штата/региона.
3. Укажите список ZIP-кодов, относящихся к создаваемой локальности (используются для подстановки в клеймы).
4. Загрузите актуальный справочник соответствия ZIP-кодов и локальностей с официального ресурса CMS:
   * [CMS Zip Code to Carrier Locality File](https://www.cms.gov/Medicare/Medicare-Fee-for-Service-Payment/ProspMedicareFeeSvcPmtGen/Downloads/Zip-Code-to-Carrier-Locality.zip)

### Шаг 2. Загрузка тарифных сеток (Fee Schedules)

Скачайте актуальный файл тарифной сетки с соответствующего регионального портала Medicare (MAC):

| Регион / Штаты | Источник данных |
| :--- | :--- |
| Kentucky, Ohio | [CGS Medicare](https://www.cgsmedicare.com/) |
| SC, NC, VA, WV, AL, GA | [Palmetto GBA](https://palmettogba.com/) |
| IL, ME, MA, MN, NH, NY, RI, VT, WI | [NGS Medicare](https://www.ngsmedicare.com/) |
| IA, KS, MO, NE | [WPS Health Solutions](https://www.wpsgha.com/) |
| AK, AZ, CA, HI, ID, MT, ND, OR, SD, UT, WA, WY | [Noridian Healthcare Solutions](https://med.noridianmedicare.com/) |
| DE, DC, MD, NJ, PA | [Novitas Solutions](https://www.novitas-solutions.com/) |
| Florida (FL-03, FL-04, FL-99) | [First Coast Service Options (FCSO)](https://medicare.fcso.com/) |

> **ВАЖНО:** Перед импортом приведите исходный файл к целевому формату системы (CSV/XLS). В большинстве случаев исходные файлы региональных порталов требуют предварительной форматирующей обработки.

### Шаг 3. Импорт данных в систему

1. Перейдите в раздел: `Setup` -> `Billing` -> `Fee Schedules`.
2. Нажмите кнопку **Data Import**.
3. Выберите сопоставленную локальность (`Locality`), формат импорта (например, `Mpfsp`), период действия тарифов (`Effective Date Start` / `End`) и путь к подготовленному `.csv` файлу.
4. Подтвердите импорт нажатием **Save**.

---

## 3. Ограничения и специфика импорта

### Коды транспортировки (Transportation Codes)
Для локальностей, обслуживаемых сервисом **Novitas Solutions**, базовый файл `Fee Schedule` не содержит транспортные коды (`R0070`, `R0075`). 
* Данные коды импортируются отдельно со специализированного раздела: [Novitas Transportation Fees](https://www.novitas-solutions.com/webcenter/portal/MedicareJL/pagebyid?contentId=00005778).

### Лабораторные исследования (Clinical Lab Fees)
1. Коды лабораторных исследований едины для всех штатов и публикуются на портале CMS:  
   [CMS Clinical Laboratory Fee Schedule](https://www.cms.gov/Medicare/Medicare-Fee-for-Service-Payment/ClinicalLabFeeSched/Clinical-Laboratory-Fee-Schedule-Files)
2. Структура файла отличается от стандартных файлов `Fee Schedule`. Перед загрузкой необходимо привести структуру файла к стандартному поддерживаемому формату (например, Noridian).

### Специфические конфигурации клиентов
* В случаях, когда клиент использует механизм `Billing Charges` вместо стандартных `Fee Schedules` для всех операций, обновление цен выполняется путем прямой актуализации таблицы `Billing Charges`.
* Обновление может выполняться массово через автоматизированные скрипты миграции либо вручную для наиболее часто используемых сервисных кодов. После проведения массового импорта обязательна валидация контрольной выборки кодов.