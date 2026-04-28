# 📦 E-Commerce & Delivery Analytics
 
> **Pet-проект** — аналитика продуктового онлайн-магазина с курьерской доставкой.  
> Все запросы написаны на **PostgreSQL** с использованием **Redash**. Визуализации собраны в дашбордах Redash.
 
---
 
## 📌 О проекте
 
В рамках проекта проведён комплексный анализ платформы e-commerce с доставкой, включая рост пользователей и курьеров, юнит-экономику, маркетинговую эффективность и удержание пользователей. Проведено вычисление метрик (`arpu`, `arppu`, `aov`, `conversion rate`, `cac`, `roi`, `retention` и другие) и их визуализация
 
**Источник данных:** Тренажер SQL karpov.courses
 
**Инструменты, навыки:** PostgreSQL · Redash · Window functions · Aggregations · CTEs · JOINs

---

## 📂 Дашборды
**Ссылки на дашборды:** [первая часть](https://redash.public.karpov.courses/public/dashboards/fMSL6Gr130go4EDCw1QVwJxLVi1kStSEfdKhGRG0?org_slug=default), [вторая часть](https://redash.public.karpov.courses/public/dashboards/wbQFlGJ88ncnJHY9xq1N3mk7KJnMg3hJG2YPEwGj?org_slug=default), [третья часть](https://redash.public.karpov.courses/public/dashboards/ODMMoGPDV39EhpUnEqo6CyDtkeho5hAbto67XJok?org_slug=default)

---

## 🐘 SQL-запросы

## 1. 📈 Рост пользователей и курьеров

### Рост общего числа пользователей и курьеров

![Рост общего числа пользователей и курьеров](screens/01_growth_users_couriers.png)

```sql
with new_user as (SELECT date,
                         count(user_id) as new_users
                  FROM   (SELECT user_id,
                                 min(date(time)) as date
                          FROM   user_actions
                          GROUP BY user_id) t1
                  GROUP BY date), new_courier as (SELECT date,
                                       count(courier_id) as new_couriers
                                FROM   (SELECT courier_id,
                                               min(date(time)) as date
                                        FROM   courier_actions
                                        GROUP BY 1) t2
                                GROUP BY 1), rezult as (SELECT date,
                               new_users,
                               new_couriers
                        FROM   new_user
                            LEFT JOIN new_courier using(date))
SELECT date,
       new_users,
       new_couriers,
       (sum(new_users) OVER (ORDER BY date))::int as total_users,
       (sum(new_couriers) OVER (ORDER BY date))::int as total_couriers
FROM   rezult
```
