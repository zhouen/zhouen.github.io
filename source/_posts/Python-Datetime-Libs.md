---
layout: post
title: Python Datetime Libs
description: This was a tech spike I did for investigating the 3 datetime libraries we were interested in a project.
date: 2019-10-02 02:39:57
comments: true
tags: [Python, Datetime]
---

### Python Date Time Libries: `pytz`, `dateutil`, and `pendulum`

Write a few paragraphs on converting UTC datetime to localized timezone datetime using the 3 libraries.
<!-- more -->
```python
import datetime
from datetime import timedelta
import pytz
import dateutil
import pendulum

# Test scenarios
# 1. Create a timezone free UTC version of now
# 2. Convert that to the following zones:
# - South Africa
# - US Eastern
# - US Pacific
# - Germany
# - Japan
# - India (Weird 30m offset)
# 3. Do the same for a specific time (2019/08/02 10:33:45+00:00)
# 3. Create a zoned UTC time on the edge of several DST switch overs
# 4. Check before and after times
# 6. Check what happens in the DST switch period (Sunday, November 3, 2:00 am LOCAL)
#
# Some details on DST in the US: https://www.timeanddate.com/time/change/usa


# fmt = '%Y-%m-%d %H:%M:%S.%f %Z%z'
fmt = '%Y-%m-%d %H:%M:%S.%f %Z%z'
utc_zone = 'UTC'
cat_zone = 'Africa/Johannesburg'
use_zone = 'US/Eastern'
ger_zone = 'Europe/Berlin'
tok_zone = 'Asia/Tokyo'
kol_zone = 'Asia/Kolkata'


def pytz_spike():

    print('pytz')
    print('========')
    # Localize timezone free UTC now
    now = datetime.datetime.utcnow()

    utc = pytz.utc
    cat = pytz.timezone(cat_zone)
    use = pytz.timezone(use_zone)
    ger = pytz.timezone(ger_zone)
    tok = pytz.timezone(tok_zone)
    kol = pytz.timezone(kol_zone)

    utc_now = utc.localize(now)
    cat_now = cat.localize(now)
    use_now = use.localize(now)
    ger_now = ger.localize(now)
    tok_now = tok.localize(now)
    kol_now = kol.localize(now)

    print(f'Localize timezone free now() to following timezone:')
    print(utc_now.strftime(fmt))
    print(cat_now.strftime(fmt))
    print(use_now.strftime(fmt))
    print(ger_now.strftime(fmt))
    print(tok_now.strftime(fmt))
    print(kol_now.strftime(fmt))

    # Localize a specific datetime, (2019/08/02 10:33:45+00:00)
    target_date = datetime.datetime(2019, 7, 21, 10, 33, 45)
    print(f'\nLocalize (2019/08/02 10:33:45+00:00) into following timezones:')
    print(utc.localize(target_date).strftime(fmt))
    print(cat.localize(target_date).strftime(fmt))
    print(use.localize(target_date).strftime(fmt))
    print(ger.localize(target_date).strftime(fmt))
    print(tok.localize(target_date).strftime(fmt))
    print(kol.localize(target_date).strftime(fmt))

    # Before DST
    print(f'\n{use_zone}: 10 January 2019, 12:34:12')
    use_before = datetime.datetime(2019, 1, 10, 12, 34, 12, 999999)
    print(use.localize(use_before).strftime(fmt))

    before_dst = datetime.datetime(2019, 3, 10, 1, 30, 59, 999999)
    print(f'\n{use_zone}: 2019-03-10, 02:00:00 clocks were turned forward 1 hour ')
    print('timezone only changes after 3:00')
    print(use.localize(before_dst).strftime(fmt))
    minutes_added = 40
    print(f'Add {minutes_added} minutes:')
    print(use.localize(before_dst+timedelta(minutes=minutes_added)).strftime(fmt))

    before_dst = datetime.datetime(2019, 11, 3, 0, 30, 59, 999999)
    print(f'\n{use_zone}: 2019-11-3, 02:00:00 clocks are turned backward 1 hour ')
    print('timezone only changes after 3:00')
    print(use.localize(before_dst).strftime(fmt))
    minutes_added = 100
    print(f'Add {minutes_added} minutes:')
    print(use.localize(before_dst + timedelta(minutes=minutes_added)).strftime(fmt))

    print('\n'+use.localize(before_dst).strftime(fmt))
    minutes_added = 40
    print(f'Add {minutes_added} minutes:')
    print(use.localize(before_dst + timedelta(minutes=minutes_added)).strftime(fmt))

    after_dst = datetime.datetime(2019, 11, 3, 2, 30, 59, 999999)
    print(f'After DST: {use.localize(after_dst).strftime(fmt)}')


def python_dateutil_spike():
    print('\ndateutil')
    print('========')
    utc = dateutil.tz.gettz(utc_zone)
    use = dateutil.tz.gettz(use_zone)
    ger = dateutil.tz.gettz(ger_zone)
    tok = dateutil.tz.gettz(tok_zone)
    kol = dateutil.tz.gettz(kol_zone)

    mar_10th = datetime.datetime(2019, 3, 10, 6, 30, 0, 0, tzinfo=utc)
    print(mar_10th.astimezone(utc).strftime(fmt))
    print(mar_10th.astimezone(use).strftime(fmt) + f' {use_zone}')
    minutes_added = 40
    print(f'Add {minutes_added} minutes')
    mar_10th_updated = mar_10th + timedelta(minutes=minutes_added)
    print(mar_10th_updated.astimezone(use).strftime(fmt) + f' {use_zone}')


def pendulum_spike():
    print('\npendulum')
    print('========')
    # Localize timezone free UTC now
    utc_now = pendulum.utcnow()
    year = utc_now.year
    month = utc_now.month
    day = utc_now.day
    hour = utc_now.hour
    minute = utc_now.minute
    second = utc_now.second
    micro_sec = utc_now.microsecond

    """
    Create datetime string, timezone free
    Localize the time string by adding timezones 
    """
    print('Localize UTC time now')
    utc = pendulum.datetime(year, month, day, hour, minute, second, micro_sec, tzinfo=utc_zone)
    print(f'{utc.strftime(fmt)}  -\t{utc_zone}')

    cat = pendulum.datetime(year, month, day, hour, minute, second, micro_sec, tzinfo=cat_zone)
    print(f'{cat.strftime(fmt)} -\t{cat_zone}')

    use = pendulum.datetime(year, month, day, hour, minute, second, micro_sec, tzinfo=use_zone)
    print(f'{use.strftime(fmt)}  -\t{use_zone}')

    ger = pendulum.datetime(year, month, day, hour, minute, second, micro_sec, tzinfo=ger_zone)
    print(f'{ger.strftime(fmt)} -\t{ger_zone}')

    tok = pendulum.datetime(year, month, day, hour, minute, second, micro_sec, tzinfo=tok_zone)
    print(f'{tok.strftime(fmt)}  -\t{tok_zone}')

    kol = pendulum.datetime(year, month, day, hour, minute, second, micro_sec, tzinfo=kol_zone)
    print(f'{kol.strftime(fmt)}  -\t{kol_zone}')

###############################################################################
    before_dst = datetime.datetime(2019, 3, 31, 1, 59, 59, 999999)
    ger = pendulum.datetime(
        before_dst.year, before_dst.month, before_dst.day, before_dst.hour,
        before_dst.minute, before_dst.second, before_dst.microsecond,
        tzinfo=ger_zone
    )
    print(
        f'\n{ger_zone}: '
        f'Sunday, 31 March 2019, 02:00:00 clocks were turned forward 1 hour'
    )
    print(ger.strftime(fmt))
    print('Add 1 microsecond')
    print(ger.add(microseconds=1).strftime(fmt))

    before_dst = datetime.datetime(2019, 10, 27, 1, 59, 59, 999999)
    ger = pendulum.datetime(
        before_dst.year, before_dst.month, before_dst.day, before_dst.hour,
        before_dst.minute, before_dst.second, before_dst.microsecond,
        tzinfo=ger_zone
    )
    print(f'\n{ger_zone}: Sunday, 27 October 2019, 03:00:00 clocks are turned backward 1 hour')
    print(ger.strftime(fmt))
    print(f'Add 85 minutes to pass 3:00')
    print(ger.add(minutes=85).strftime(fmt))
###############################################################################
    before_dst = datetime.datetime(2019, 1, 10, 12, 34, 12, 999999)
    use = pendulum.datetime(
        before_dst.year, before_dst.month, before_dst.day, before_dst.hour,
        before_dst.minute, before_dst.second, before_dst.microsecond,
        tzinfo=use_zone
    )
    print(f'\n{use_zone}: 10 January 2019, 12:34:12')
    print(use.strftime(fmt))

    before_dst = datetime.datetime(2019, 3, 10, 1, 30, 59, 999999)
    use = pendulum.datetime(
        before_dst.year, before_dst.month, before_dst.day, before_dst.hour,
        before_dst.minute, before_dst.second, before_dst.microsecond,
        tzinfo=use_zone
    )
    print(f'\n{use_zone}: Sunday, 10 March 2019, 02:00:00 clocks were turned forward 1 hour')
    print('Timezone starts changing after 2:00')
    print(use.strftime(fmt))
    minutes_added = 40
    print(f'Add {minutes_added} minutes:')
    print(use.add(minutes=minutes_added).strftime(fmt))

    after_dst = datetime.datetime(2019, 4, 5, 12, 34, 12, 999999)
    use = pendulum.datetime(
        after_dst.year, after_dst.month, after_dst.day, after_dst.hour,
        after_dst.minute, after_dst.second, after_dst.microsecond,
        tzinfo=use_zone
    )
    print(f'\n{use_zone}: 5 April 2019, 12:34:12')
    print(use.strftime(fmt))

    before_dst = datetime.datetime(2019, 11, 3, 0, 40, 00, 0)
    use = pendulum.datetime(
        before_dst.year, before_dst.month, before_dst.day, before_dst.hour,
        before_dst.minute, before_dst.second, before_dst.microsecond,
        tzinfo=use_zone
    )
    print(f'\n{use_zone}: Sunday, 3 November 2019, 02:00:00 clocks are turned backward 1 hour ')
    print(use.strftime(fmt))
    minutes_added = 85
    print(f'Add: {minutes_added} minutes')
    print(use.add(minutes=minutes_added).strftime(fmt))

    before_dst = datetime.datetime(2019, 11, 3, 0, 40, 00, 0)
    use = pendulum.datetime(
        before_dst.year, before_dst.month, before_dst.day, before_dst.hour,
        before_dst.minute, before_dst.second, before_dst.microsecond,
        tzinfo=use_zone
    )
    print('\n' + use.strftime(fmt))
    minutes_added = 35
    print(f'Add: {minutes_added} minutes')
    print(use.add(minutes=minutes_added).strftime(fmt))

    after_dst = datetime.datetime(2019, 11, 3, 3, 44, 12, 999999)
    use = pendulum.datetime(
        after_dst.year, after_dst.month, after_dst.day, after_dst.hour,
        after_dst.minute, after_dst.second, after_dst.microsecond,
        tzinfo=use_zone
    )
    print(f'\n{use_zone}: {use.strftime(fmt)}')
###############################################################################


if __name__ == "__main__":
    pytz_spike()
    python_dateutil_spike()
    pendulum_spike()

```
