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

## Ví dụ về sử dụng selectattr() để lọc ra những bóng đèn và công tắc đang ở chế độ on
```
{% set domains = ['light', 'switch'] %}
{{ states|selectattr('domain', 'in', domains )| selectattr('state', 'equalto', 'on')|map(attribute='entity_id')|list|join(', ') }}
```

## Ví dụ về sử dụng rejectattr() để lọc ra những entity không phải là bóng đèn và công tắc
```
{% set excluded_domains = ['light', 'switch'] %}
{% set entities = states | rejectattr('domain', 'in', excluded_domains) | map(attribute='entity_id') | list %}
{{ entities }}
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
{# convert timetamp to datetime str #}s
# filter only
timestamp_local  =>{{ 1748146555|timestamp_local }}
timestamp_utc =>{{ 1748146555|timestamp_utc }}
timestamp_custom filter =>{{ 1748146555|timestamp_custom('%d-%m-%y %H:%M:%S') }}
# output
timestamp_local  =>2025-05-25T11:15:55+07:00
timestamp_utc =>2025-05-25T04:15:55+00:00
timestamp_custom filter =>25-05-25 11:15:55
```
## Sử dụng isoformat() để convert một datetime object về dạng ISO 8601 str (có T ở giữa ngày và giờ):
```
  {{ states.light.phong_hoc.last_changed.isoformat() }}

```

# time_pattern tigger
## fire mỗi 5 giây (Các giây thứ 0, 5, 10...)
```
- id: trigger fire every 1 minute
  alias: trigger fire every 1 minute
  mode: single
  triggers: 
    trigger: time_pattern
    seconds: /5
```
## fire mỗi 1 phút
```
- id: trigger fire every 1 minute
  alias: trigger fire every 1 minute
  mode: single
  triggers: 
    trigger: time_pattern
    minutes: /1
```
## trigger vào phút thứ 0 của mỗi giờ
```
trigger:
  - platform: time_pattern
    minutes: "0"
```
## fire vào phút thứ 15 của mỗi giờ
```
- id: trigger fire every 1 minute
  alias: trigger fire every 1 minute
  mode: single
  triggers: 
    trigger: time_pattern
    minutes: 10
  condition: []
```
## fire vào phút thứ 0, 15, 17 của mỗi giờ

> [!CAUTION]
> Hiện tại time_trigger không hỗ trợ viết kiểu **minutes: "0,15,17"**
Có thể dùng nhiều time_trigger để đặt được mục đích:
```
trigger:
  - platform: time_pattern
    minutes: "0"
  - platform: time_pattern
    minutes: "15"
  - platform: time_pattern
    minutes: "17"
```
hoặc sử dụng template trigger:
```
trigger:
  - platform: template
    value_template: >
      {% set m = now().minute %}
      {{ m in [0, 15, 17] }}

```

> [!TIP]  
>Lưu ý: template trigger sẽ được đánh giá mỗi phút, vì vậy nó phù hợp cho các kiểm tra theo phút.


## fire trong giờ hành chính
```
- alias: 'Kich hoat dau moi gio trong gio hanh chinh'
  description: 'Chay automation vao 00 phut moi gio tu 8h sang den 5h chieu, tu T2 den T6'
  trigger:
    - platform: time_pattern
      minutes: 0 # Kich hoat vao phut thu 0 cua moi gio
  condition:
    - condition: time
      # Chi chay tu 8h sang den 5h chieu
      after: '08:00:00'
      before: '17:01:00' # Đặt 17:01:00 để đảm bảo bao gồm cả 17:00:00
      # Chi chay tu Thu Hai den Thu Sau
      weekday:
        - mon
        - tue
        - wed
        - thu
        - fri
```

## fire mỗi phút một phần chỉ trong giờ thứ 3
```
  triggers:
    - trigger: time_pattern
      # Trigger once per minute during the hour of 3
      hours: "3"
      minutes: "*"
```

## Tự động tắt đèn sau một khảng thời gian tính từ lúc bật
```
- id: tu dong tat den sau 30s bat
  alias: tu dong tat den sau 30s bat
  triggers: 
    - trigger: state
      entity_id: light.phong_hoc
      to: "on"
  conditions: []
  actions: 
    - action: input_datetime.set_datetime
      target:
        entity_id: input_datetime.time_turn_off_light
      data:
        # datetime: >
        #   {% set NEXT_1_MINUTE = now() + timedelta(minutes = 1) %}
        #   {{ NEXT_1_MINUTE.strftime('%Y-%m-%d %H:%M:%S')}}
        datetime: >
          {% set MOUNT_OF_TIME_TO_TURN_OF_LIGHT = inpu %}
          {% set NEXT_1_MINUTE = now() + timedelta(minutes = 1) %}
          {{ NEXT_1_MINUTE.strftime('%Y-%m-%d %H:%M:%S')}}
- id: bat den khi dat den thoi gian quy dinh
  alias: bat den khi dat den thoi gian quy dinh
  triggers: 
    - trigger: time
      at: input_datetime.time_turn_off_light
  conditions: []
  actions: 
    - action: light.turn_off
      target:
        entity_id: light.phong_hoc
```
##  Điều khiển đèn bằng webhook kết hợp với choose
```
alias: Điều khiển đèn bằng webhook
trigger:
  - platform: webhook
    webhook_id: some_hook_id
action:
  - choose:
      - conditions:
          - condition: template
            value_template: "{{ trigger.json.light == 'on' }}"
        sequence:
          - service: light.turn_on
            target:
              entity_id: light.phong_khach
      - conditions:
          - condition: template
            value_template: "{{ trigger.json.light == 'off' }}"
        sequence:
          - service: light.turn_off
            target:
              entity_id: light.phong_khach
```
## Sử Dụng webhook để điều khiển bóng đèn ( có gửi kèm thông tin )
```
alias: Điều khiển đèn với màu sắc và độ sáng
trigger:
  - platform: webhook
    webhook_id: turn_light_on
action:
  - service: light.turn_on
    target:
      entity_id: light.phong_khach  # 👉 thay bằng entity của bạn
    data:
      rgb_color: "{{ trigger.json.rgb_color }}"
      brightness: "{{ trigger.json.brightness }}"
với request như sau:
curl -X POST -H 'Content-Type: application/json' -d '{"rgb_color": [88,255,126], "brightness" : 50}' http://ducvuong25.ddns.net:8123/api/webhook/turn_light_on
```
## Thay đổi màu sắc bóng đèn bằng webhook + choose/conditions
```
hiện tại automation của tôi đã hoạt động rồi. 
- id: webhoook redo 
  alias: webhook redo
  triggers: 
    - trigger: webhook
      webhook_id: "change-light-color"
      allowed_methods:
        - GET
  conditions: []
  actions: 
    - choose:
      - conditions: 
        - condition: template
          value_template: "{{ trigger.query.color_name == 'red' }}"
        sequence: 
          - action: light.turn_on
            target:
              entity_id: light.phong_hoc
            data:
              rgb_color: [255,0,0]
      - conditions: 
        - condition: template
          value_template: "{{ trigger.query.color_name == 'yellow' }}"
        sequence: 
          - action: light.turn_on
            target:
              entity_id: light.phong_hoc
            data:
              rgb_color: [255,255,0]
```

## Dùng webhook thay đổi bóng đèn ( Phiển bản cải tiến: khai báo automation variable level kiểu dic)
```
- id: webhook_redo
  alias: webhook redo
  trigger:
    - platform: webhook
      webhook_id: "change-light-color"
      allowed_methods:
        - GET
  variables:
    color_map:
      red: [255, 0, 0]
      yellow: [255, 255, 0]
      green: [0, 255, 0]         # thêm màu nếu cần
      blue: [0, 0, 255]
  condition: []
  action:
    - choose:
        - conditions:
            - condition: template
              value_template: "{{ trigger.query.color_name in color_map }}"
          sequence:
            - service: light.turn_on
              target:
                entity_id: light.phong_hoc
              data:
                rgb_color: "{{ color_map[trigger.query.color_name] }}"
    - default:
        - service: notify.persistent_notification
          data:
            message: "Màu không hợp lệ: {{ trigger.query.color_name }}"

```
> [!TIP]
> https://chatgpt.com/share/685966bc-e588-800d-9520-b03048b125a1
## Kiểm tra 1 string có nằm trong các option của input_select hay không:
```
{{ 'study' in state_attr('input_select.den_hoc_sence','options') }}
```
## condition
### time condition
```
- id: turn_light_on_when_has_get_request
  alias: turn_light_on_when_has_get_request
  triggers: 
    - trigger: webhook
      webhook_id: "turn_light_on"
      allowed_methods:
        - "POST"
      local_only: false
  conditions: 
    - condition: time
      after: "15:50:00"
  actions: 
    - action: light.turn_on
      target: 
        entity_id: light.phong_hoc
      data:
        brightness: "{{ trigger.json.brightness }}"
        rgb_color: "{{ trigger.json.rgb_color }}"
    - action: notify.persistent_notification
      data:
        message: >
          "{{ trigger.json }}"
```
### state condition
```
todo
```

```
### template sensor: Cảm biến ảo "Tuổi thọ lõi lọc nước" (Giảm dần theo ngày)
```
template:
  - sensor:
      - name: "Phần trăm lõi lọc còn lại"
        unique_id: phan_tram_loi_loc_nuoc
        unit_of_measurement: "%"
        icon: "mdi:water-percent"
        state: >
          {% set ngay_lap = states('input_datetime.ngay_thay_loi') | as_datetime %}
          {% set hom_nay = now() %}
          {% set so_ngay_da_dung = (hom_nay - ngay_lap).days %}
          {% set tong_han_dung = 180 %}
          
          {# Tính toán phần trăm còn lại #}
          {% set phan_tram = ((tong_han_dung - so_ngay_da_dung) / tong_han_dung * 100) | round(0) %}
          
          {# Đảm bảo con số không bị âm #}
          {{ [0, phan_tram] | max }}
```

