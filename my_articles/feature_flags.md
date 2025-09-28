### settings table example

```
id  | team                     | type       | feature             | key                                              | value  | expiry_date  | description
1   | DeliveryCourier          | tech, log  | peak_mode           | disable_full_attribute_order_delivery_history    |  '0',  | null         | 'Вкл/выкл логирование в историю только изменение статусов для доставки'
2   | NeoShopAppIntegration    | tech       | peak_mode           | peak_lite_mode_neo_shop_app                      |  '0',  | null         | 'Отключение функций нового мобильного приложения. 0 - всё работает стандартно/1 - отключили функциональность'
3   | Infra                    | tech       | log                 | send_log_to_sentry                               |  '1',  | null         | 'Вкл/выкл отправку логов в Sentry'
4   | DeliveryCourier          | product    | delivery_providers  | is_yandex_delivery_allowed                       |  '0',  | null         | 'Разрешена ли доставка через яндекс'
5   | DeliveryCourier          | tech       | delivery_providers  | is_magnit_delivery_log_enabled                   |  '1',  | null         | 'Флаг включения логов по провайдеру доставки Магнит. 0 Выкл / 1 Вкл'
```

```
id  | team                     | tags                           | key                                              | value  | expiry_date  | description
1   | DeliveryCourier          | tech, peak_mode                | disable_full_attribute_order_delivery_history    |  '0',  | null         | 'Вкл/выкл логирование в историю только изменение статусов для доставки'
2   | NeoShopAppIntegration    | tech, log                      | peak_lite_mode_neo_shop_app                      |  '0',  | null         | 'Отключение функций нового мобильного приложения. 0 - всё работает стандартно/1 - отключили функциональность'
3   | Infra                    | tech                           | send_log_to_sentry                               |  '1',  | null         | 'Вкл/выкл отправку логов в Sentry'
4   | DeliveryCourier          | product, delivery_providers    | is_yandex_delivery_allowed                       |  '0',  | null         | 'Разрешена ли доставка через яндекс'
5   | DeliveryCourier          | tech, delivery_providers       | is_magnit_delivery_log_enabled                   |  '1',  | null         | 'Флаг включения логов по провайдеру доставки Магнит. 0 Выкл / 1 Вкл'
````
