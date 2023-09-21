---
title: "Логистическая система"
date: 2023-07-03T13:41:20+03:00
slug: "logistic system"
categories: ["fullstack", ".NET", "java", "android"]
description: Проект логистической системы, позволяющий автоматизировать процессы учета выполнения и формирования маршрутных листов. Реализован в рамках дипломной работы.
---

## Описание и решаемые задачи

Система предназначена для автоматизации процесса формирования оптимального маршрута, а также для автоматизации процесса распределения заказов между водителями.

- Система позволила автоматизировать процесс распределения заказов, тем самым разгрузив операторов;
- обеспечила хранение и обмен данных;
- позволила оптимизировать маршрут следования.

## Реализация

Система была реализована в двух приложениях, настольное и мобильное. Настольное приложение предназначено для оператора. Мобильное приложение предназначено для водителя.

Мобильное приложение было запрограммировано в Android Studio на языке Java.

{{< figure src="/images/mobile.png" title="Окно мобильного приложения с возможностью выбора заказа" >}}

Для отображения карты использовалось API Yandex maps.

```java
    /**
     * Начинает пострение маршрута из нынешнего расположения, включает поиск координаты расположения по адресу
     * @param position Адрес точки назначения
     */
    public void routingStart(String position) {
        searchManager.submit(
                position, VisibleRegionUtils.toPolygon(mapView.getMap().getVisibleRegion()),
                new SearchOptions(), this);
        DatabaseReference reference = FirebaseDatabase.getInstance().getReference("/Orders");
        reference.child(Long.toString(currentItem.Id)).child("Status").setValue((long)1);
        reference.child(Long.toString(currentItem.Id))
                .child("TakeDate").setValue(Calendar.getInstance().getTime().toString());
        findViewById(R.id.btnAuth).setEnabled(false);
        findViewById(R.id.data_items).setVisibility(View.GONE);
        findViewById(R.id.confirmMenu).setVisibility(View.VISIBLE);
        ((TextView) findViewById(R.id.txtMainDir)).setText(String
                .format("%s, %s", currentItem.CurrentOrder.Address, currentItem.CurrentOrder.City));
    }
```

#### Исходный код метода, который вызывается после выбора заказа.

```java
    /**
     * Расчет расстояния по формуле Хаверсина
     * @param from Первая точка
     * @param to Вторая точка
     * @return Расстояние между точками в километрах
     */
    public double distance(Point from, Point to){
        final double R = 6372.8;
        double dLat = Math.toRadians(to.getLatitude() - from.getLatitude());
        double dLon = Math.toRadians(to.getLongitude() - from.getLongitude());
        double lat1 = Math.toRadians(from.getLatitude());
        double lat2 = Math.toRadians(to.getLatitude());
        double a = Math.pow(Math.sin(dLat/2),2) + Math.pow(Math.cos(lat1)*Math.cos(lat2)*Math.sin(dLon/2),2);
        double c = 2*Math.asin(Math.sqrt(a));
        return R*c;
    }
```

#### Исходный код метода, который предназначен для расчета дистанции на планете.

Настольное приложение было запрограммировано в среде Visual Studio на языке C#.

{{< figure src="/images/pc1.png" title="Окно настольного приложения" >}}


```c#
    /// <summary>
    /// Формирует список наименее занятых водителей для составления маршрутного листа
    /// </summary>
    async private void LoadData()
    {
        CollectionReference ordersRef = App.db.Collection("Orders");
        CollectionReference usersRef = App.db.Collection("Users");
        snapshotOrders = (await ordersRef.GetSnapshotAsync()).Documents;
        snapshotUsers = (await usersRef.GetSnapshotAsync()).Documents;
        var userOrders = new Dictionary<string, int>();
        // Составляет массив всех водителей
        foreach (var user in snapshotUsers)
        {
            if (user.GetValue<Int64>("Role") == 1) userOrders.Add(user.Id, 0);
            foreach (var order in snapshotOrders)
            {
                if (user.Id == order.GetValue<string>("UserId") 
                && order.GetValue<Int64>("Status") == 0)
                {
                    userOrders[user.Id]++;
                }
            }
        }
        // Формирует список водителей, с учетом количества заказов
        foreach (var user in userOrders.OrderBy(p => p.Value))
        {
            if (user.Value <= 4)
            {
                var item = new ComboBoxItem();
                item.Content = App.Users[user.Key] + " " 
                    + user.Value + " активных заказов.";
                item.Tag = user.Key;
                CBUser.Items.Add(item);
            }
        }
        foreach (DocumentSnapshot document in snapshotOrders)
        {
            if ((string)document.ToDictionary()["UserId"] == "")
            {
                Orders.Children.Add(new UCOrderItemSelect(document));
            }
        }
    }
```

#### Исходный код метода, который формирует список наименее занятых водителей.

## Полный исходный код

- Полный исходный код настольного приложения можно посмотреть [здесь](https://github.com/Mofrog).
- Полный исходный код мобильного приложения можно посмотреть [здесь](https://github.com/Mofrog).