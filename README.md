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

