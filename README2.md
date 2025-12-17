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
