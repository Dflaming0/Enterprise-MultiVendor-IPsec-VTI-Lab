# Lab 1: Verification & Implementation Steps

## 1. Baseline Network Setup & NAT Exemption
* **VLAN Segmentation:** На коммутаторе `SW-CORE-HQ` разграничены зоны `MGMT` (192.168.2.0/24), `SRV` (192.168.3.0/24), `USR` (192.168.1.0/24) и `DMZ`.
* **ISP Configuration:** На `CS-ISP` настроена статическая маршрутизация, интерфейсы и PAT для выхода хостов в интернет.
* **NAT Exemption:** Для предотвращения трансляции межофисного трафика в PAT на `CS-ISP` сформирован ACL (NAT Exemption), исключающий подсети `10.0.0.0/8` и `192.168.0.0/16` из динамической трансляции.

---

## 2. Check Point Management & Management-over-NAT
1. **Deployment:** На узлах `CP-GW-HQ` и `CP-SMS-HQ` развернута Gaia OS R81.20. Первоначальная настройка сетевых интерфейсов и параметров SMS/GW выполнена через First Time Configuration Wizard (Gaia WebUI).
2. **SIC & Objects Initialization:** В SmartConsole установлен SIC-канал с шлюзом `CP-GW-HQ`. Созданы сетевые объекты зон `MGMT`, `SRV`, `USR`, `DMZ`.
3. **Control Plane Rules:** Оформлены правила Access Control для системного трафика между SMS и шлюзами по портам SIC и логирования: `18191` (CPMI), `18192` (CPD/SIC), `18186`, `18211`, `18210`, `18209`, `257` (FW1_log).
4. **Branch GW Management over NAT:**
   * Для подключения удаленного шлюза `CP-GW-BR1` за NAT-устройством настроена трансляция Hide NAT адреса SMS в роутабельный адрес `10.0.2.100`.
   * При компиляции политики SMS автоматически транслирует свой управляющий IP-адрес для `CP-GW-BR1`, исключая сброс пакетов и некорректное определение источника при загрузке политики.

---

## 3. Route-Based Site-to-Site IPsec VPN & OSPF
1. **VTI & Loopback Configuration:**
   * На `CP-GW-HQ` и `CP-GW-BR1` созданы виртуальные туннельные интерфейсы (VTI): `vti1` (`172.16.1.1/30` <-> `172.16.1.2/30`).
   * Для стабилизации работы процессов OSPF на обоих шлюзах настроены интерфейсы `Loopback0`, адреса которых используются в качестве статического `OSPF Router-ID`.
2. **VPN Community Setup:**
   * Создана топология Star (Center-Satellite Community) с центром `CP-GW-HQ` и сателлитом `CP-GW-BR1`.
   * **VPN Domain:** В качестве VPN Domain шлюзов назначена **пустая группа (Empty Group)**, что переводит туннелирование строго в режим Route-Based (VTI) и исключает конфликт с Policy-Based шифрованием.
   * **Encryption Parameters:** Зафиксирован профиль Suite B, протокол IKEv2, режимы Phase 1/Phase 2 сгенерированы как *One VPN Tunnel per Gateway pair*.
3. **Dynamic Routing (OSPF):**
   * В Gaia WebUI запущен процесс OSPF, в качестве `Area 0.0.0.0` добавлен интерфейс `vti1` с `cost 1`.
   * Настроена редистрибуция локальных сетей (Route Redistribution) для автоматического обмена маршрутами между HQ и BR1.
   * В Access Policy добавлено правило, разрешающее IP-протокол `89` (OSPF) между VTI-интерфейсами peer-шлюзов.

---

## 4. Identity Awareness & Active Directory Integration
1. **Active Directory Setup:**
   * На сервере `DC` (192.168.3.1) развернут домен `dofi.lab`.
   * Создана структура OU `OU=Company-HQ`, группа пользователей `Users-HQ` и учетная запись `LAU` (LDAP Account Unit).
   * Для работы механизма WMI/Event Log чтения учетная запись `LAU` добавлена в доменную группу **Event Log Readers**.
2. **Identity Collector Integration:**
   * На шлюзе `CP-GW-HQ` активирован блейд Identity Awareness, выбран метод авторизации **Identity Collector**.
   * В SmartConsole создан объект **LDAP Account Unit** (`AD_Microsoft`, домен `dofi.lab`, порт `389`, привязка к учетке `LAU`).
   * На хосте `Jumphost` развернут Identity Collector, настроен опрос журналов событий безопасности домен-контроллера `DC` (Query Pools).
3. **Identity-Based Policy Enforcement:**
   * В SmartConsole созданы объекты **Access Role** с привязкой к доменным учетным записям (на примере пользователя `Katya Brusnika`).
   * В правила Access Control и Application Control внесены политики разграничения доступа (блокировка категорий Web/YouTube для роли `Katya Brusnika`).
   * Успешная идентификация пользователя и корректная отработка правил блокировки подтверждены записями в **SmartConsole Logs & Monitor**.
