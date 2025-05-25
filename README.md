## Đếm những sensor có attirubte 'device_class' là 'timestamp' 
```
{{ states.sensor | selectattr('attributes.device_class', 'equalto', 'timestamp') | list |count }}
```

## Đên xem có bao nhiều đèn đang được bật
```
{{ states.light | selectattr('state', 'equalto', 'on') | list | length }}
```
## Lấy ra bóng bóng đèn đang dược bạt có index = 0
```
{{ states.light | selectattr('state', 'equalto', 'on') | list | first }}
```
## hoặc dùng dùng index thông qua biến tạm
```
{% set lights_on = states.light | selectattr('state', 'equalto', 'on') | list %}
{{ lights_on[0] if lights_on|length > 0 else 'Không có đèn nào đang bật' }}
```

## Ví dụ về sử dụng map trên 1 list state object để lấy ra entity_id
```
{{ states.light 
   | selectattr('state', 'equalto', 'on') 
   | map(attribute='entity_id') 
   | list 
   | join(', ')
}}
```

## Ví dụ về sử dụng map trên 1 list state object để lấy ra device_class ( property trực tiếp của state.attributes)
```
{{ states.light 
   | map(attribute='attributes.friendly_name') 
   | list 
}}
```
## Dùng strptime() để convert 1 string tùy ý về datetime object
```
{{ strptime('26&10&1992 09:15:30', '%d&%m&%Y %H:%M:%S') }}
```

## Dùng as_local() để lấy ra được thời gian báo thức tiếp theo theo local time
```
{% set next_alarm_in_utc_time_str =  states('sensor.sm_s911b_next_alarm') %}
{% set next_alarm_in_utc_time =  as_datetime(next_alarm_in_utc_time_str) %}
{% set next_alarm_in_local_time = as_local(next_alarm_in_utc_time) %}
{{ next_alarm_in_local_time }}
```
## Tính tuổi bản thân bằng relative_time() và time_since()
```
{% set MY_BIRTHDAY = as_datetime('1992-10-26T09:15:00+07:00') %}
{{ relative_time(MY_BIRTHDAY) }}
{{ time_since(MY_BIRTHDAY,4) }}

return:
33 years
32 years 7 months 9 days 2 hours
```
## Sử dụng các filter khác nhau để convert UTC timetamp thành string format:
```
{# convert timetamp to datetime str #}
# filter only
timestamp_local  =>{{ 1748146555|timestamp_local }}
timestamp_utc =>{{ 1748146555|timestamp_utc }}
timestamp_custom filter =>{{ 1748146555|timestamp_custom('%d-%m-%y %H:%M:%S') }}
# output
timestamp_local  =>2025-05-25T11:15:55+07:00
timestamp_utc =>2025-05-25T04:15:55+00:00
timestamp_custom filter =>25-05-25 11:15:55
```
## Sử dụng isoformat() để convert một datetime object về dạng ISO 8601 str:
```
  {{ states.light.phong_hoc.last_changed.isoformat() }}

```
