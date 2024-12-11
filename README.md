# Лабараторная работа 5
## Задача 1
1. Я создал файл pre-commit в /Users/arpo/git-lab/.git/hooks
2. Написал скрипт
```
#!/bin/bash
FILES=$(git diff --cached --name-only)
flag=0
for val in $FILES; do
	if ! [ -s "$val" ]; then
		echo "Error: File '$val' does not exist or is empty."
		flag=1
	fi
	if [[ "$val" != *.txt ]]; then
        echo "Error: File '$val' is not a .txt file."
        flag=1
    fi
done

if [[ $flag = 1 ]]; then
	exit 1
fi
```
3. Проверка:
<img width="536" alt="Снимок экрана 2024-12-10 в 21 38 02" src="https://github.com/user-attachments/assets/ed695b05-978c-4a13-a761-4eab69356230">

## Задача 2

<img width="689" alt="Снимок экрана 2024-12-11 в 14 52 13" src="https://github.com/user-attachments/assets/18547f95-0c58-4c38-90f7-9a005ec1487e">
<img width="690" alt="Снимок экрана 2024-12-11 в 14 52 26" src="https://github.com/user-attachments/assets/7eeada6b-057d-4409-a1ca-2b178a5253bf">
