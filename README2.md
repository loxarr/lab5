# Вариант 13

## IDEF1X 
<img width="3355" height="2956" alt="image" src="https://github.com/user-attachments/assets/ce626c29-6e31-4e83-9983-3b9ef7febf77" />

### Описание связей:
- Матч 1–M Стадион
- Матч 1–M Участие_команды_в_матче
- Команда 1–M Участие_команды_в_матче
- Матч 1–M Участие_игрока_в_матче
- Игрок 1–M Участие_игрока_в_матче
- Команда 1–M Участие_игрока_в_матче
- Команда 1–M Контракт_игрока
- Игрок 1–M Контракт_игрока

## SQL запросы
1. Все матчи на заданном стадионе (пример stadium_id = 2)
```
SELECT
    m.match_id,
    m.date,
    m.status,
    m.home_score,
    m.guest_score
FROM match AS m
WHERE m.stadium_id = 2
ORDER BY m.date;
```
<img width="355" height="242" alt="Снимок экрана 2025-12-17 в 16 30 11" src="https://github.com/user-attachments/assets/717a8bb4-3a62-4610-a136-d6e01c28a269" />

2. Отчёт по стадиону (кол-во матчей, победы хозяев/гостей)
```
SELECT
    COUNT(*) AS total_matches,
    SUM(CASE WHEN m.home_score > m.guest_score THEN 1 ELSE 0 END) AS home_wins,
    SUM(CASE WHEN m.home_score < m.guest_score THEN 1 ELSE 0 END) AS guest_wins
FROM match AS m
WHERE m.stadium_id =  2
  AND m.status = 'played';
```
<img width="235" height="45" alt="Снимок экрана 2025-12-17 в 16 34 37" src="https://github.com/user-attachments/assets/34c6d584-cc9a-4979-9fe8-2529c3a809d0" />

3. Игроки, забивавшие мячи на этом стадионе, по командам
```
SELECT
    t.name        AS team_name,
    p.full_name   AS player_name,
    SUM(pm.goal_scored) AS goals
FROM match AS m
JOIN player_match AS pm
    ON pm.match_id = m.match_id
JOIN player AS p
    ON p.player_id = pm.player_id
JOIN team AS t
    ON t.team_id = pm.team_id
WHERE m.stadium_id =  2
  AND m.status = 'played'
  AND pm.goal_scored > 0
GROUP BY t.name, p.full_name
ORDER BY t.name, goals DESC;
```
<img width="363" height="246" alt="Снимок экрана 2025-12-17 в 16 36 58" src="https://github.com/user-attachments/assets/12883bb4-3fdb-4322-867a-16e6af898857" />

4. Стадионы, где проводились встречи
```
SELECT
    s.stadium_id,
    s.name,
    s.city,
    COUNT(m.match_id) AS games_played
FROM stadium AS s
JOIN match AS m
    ON m.stadium_id = s.stadium_id
WHERE m.status = 'played'
GROUP BY s.stadium_id, s.name, s.city
ORDER BY s.name;
```
<img width="387" height="113" alt="Снимок экрана 2025-12-17 в 16 37 57" src="https://github.com/user-attachments/assets/26e21fdf-e3b4-445d-acd5-918ec356ecd6" />

5. Даты встреч команды, её противник и счёт
```
SELECT
    m.date,
    t_self.name  AS team_name,
    t_opp.name   AS opponent_name,
    m.home_score,
    m.guest_score
FROM match AS m
JOIN team_match AS tm_self
    ON tm_self.match_id = m.match_id
JOIN team AS t_self
    ON t_self.team_id = tm_self.team_id
JOIN team_match AS tm_opp
    ON tm_opp.match_id = m.match_id
   AND tm_opp.team_id <> tm_self.team_id
JOIN team AS t_opp
    ON t_opp.team_id = tm_opp.team_id
WHERE t_self.name = 'НПО «Зуев-Павлова»'
AND m.status = 'played'
ORDER BY m.date;
```
<img width="447" height="69" alt="Снимок экрана 2025-12-17 в 20 23 54" src="https://github.com/user-attachments/assets/5383f47b-8414-4a45-afd7-d2e0e2bc7450" />

6. ФИО и номера игроков, участвовавших во встрече
```
SELECT DISTINCT
    p.full_name,
    p.number
FROM match AS m
JOIN team_match AS tm
    ON tm.match_id = m.match_id
JOIN team AS t
    ON t.team_id = tm.team_id
JOIN player_match AS pm
    ON pm.match_id = m.match_id
   AND pm.team_id  = t.team_id
JOIN player AS p
    ON p.player_id = pm.player_id
WHERE t.name =  'Каменск-Уральский металлургический завод'
  AND m.date = '2026-01-05'
ORDER BY p.full_name;
```
<img width="278" height="243" alt="Снимок экрана 2025-12-17 в 20 28 28" src="https://github.com/user-attachments/assets/e60a7119-1c4c-49e8-b34e-097e3313cc83" />

7. Результативность данного игрока в данной встрече
```
SELECT
    p.full_name,
    t.name      AS team_name,
    m.date,
    pm.goal_scored
FROM match AS m
JOIN team_match AS tm
    ON tm.match_id = m.match_id
JOIN team AS t
    ON t.team_id = tm.team_id
JOIN player_match AS pm
    ON pm.match_id = m.match_id
   AND pm.team_id  = t.team_id
JOIN player AS p
    ON p.player_id = pm.player_id
WHERE t.name = 'Первый завод'
  AND m.date = '2026-06-14'
  AND p.full_name = 'Ладимир Александрович Маслов';
```
<img width="423" height="41" alt="Снимок экрана 2025-12-17 в 20 45 48" src="https://github.com/user-attachments/assets/15114e25-7eef-4cc0-9a33-fef718cf6380" />

8. Цена билета на матч указанных команд
```
SELECT DISTINCT
    m.match_id,
    m.date,
    s.name  AS stadium_name,
    m.ticket_price
FROM match AS m
JOIN stadium AS s
    ON s.stadium_id = m.stadium_id
JOIN team_match AS tm1
    ON tm1.match_id = m.match_id
JOIN team AS t1
    ON t1.team_id = tm1.team_id
JOIN team_match AS tm2
    ON tm2.match_id = m.match_id
   AND tm2.team_id <> tm1.team_id
JOIN team AS t2
    ON t2.team_id = tm2.team_id
WHERE t1.name = 'Каменск-Уральский металлургический завод'
  AND t2.name = 'ОАО «Тетерин-Соболева»'
  AND m.date = '2026-01-05';
```
<img width="334" height="39" alt="Снимок экрана 2025-12-17 в 20 44 21" src="https://github.com/user-attachments/assets/75c29358-e3e8-4419-a284-7443686674fe" />

