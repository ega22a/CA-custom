# Custom CRT chain
> **Для того, чтобы использовать этот метод электронного документооборота, должно быть выпущено соответсвующее Положение о внутреннем электронном документообороте с использованием неквалифицированных электронных подписей! Пример Положения в вики.**

Серверное API для создания самостоятельной зоны выдачи сертификатов для подписи документов. Согласно российскому законодательству, генерируемые подписи являются *неквалифицированными электронными подписями*.

## Установка API на сервер

Для того, чтобы установить API на ваш сервер, вам нужно, чтобы на вашем сервере были установлены:
- Apache2 **с включеным mod_rewrite** (*Nginx тоже можно, но на нем не тестируется*);
- MySQL (*или PostgreSQL, но это на ваш вкус*);
- PHP 7.\* или 8.\* с модулями:
 - php-json;
 - php-mbstring;
 - php-gd;
 - php-openssl.

Скачайте сервер и распакуйте его в необходимую директорию (желательно туда, где будет запущен **домен третьего уровня или второго**).
Также, нужно создать директорию, в которой будут храниться все подписи. **У этой директории должны быть права доступа только серверу**. После того, как был распакован сервер, нужно зайти в директорию `configurations/` и переименовать файл `const.original.ini` в `const.ini`. В этом файле нужно задать `passphrase` для того, чтобы впервые запустить систему. Пример файла `const.ini` на момент установки системы:
```
[first_start]
is_first_start = true
passphrase = null
```

После того, как вы задали `passphrase`, нужно сделать запрос. Пример запроса:
```bash
curl --location --request POST 'https://example.com/api/admin/execute' \
--form 'passphrase="some_passphrase"' \
--form 'lastname="Иванов"' \
--form 'firstname="Иван"' \
--form 'patronymic="Иванович"' \
--form 'countryName="RU"' \
--form 'stateOrProvinceName="Свердловская область"' \
--form 'localityName="Нижний Тагил"' \
--form 'organizationName="Sarcasm Team"' \
--form 'organizationalUnitName="Отдел разработки и сопровождения"' \
--form 'email="abuse@example.com"' \
--form 'db_host="localhost"' \
--form 'db_login="login"' \
--form 'db_password="password"' \
--form 'db_name="db_name"' \
--form 'folder_path="/path/to/folder"'
```
После удачного отправления запроса, сервер ответит JSON-объектом, в котором будут данные для входа администратора. Пример ответа:
```json
{
	"status": "OK",
	"admin": {
		"email": "abuse@example.com",
		"password": "some_password"
	},
	"message": "ROOT_ZONE_IS_NOT_SET"
}
```
В дальнейшем сервер всегда будет отвечать JSON-объектами (не касается скачивания файлов). Дальнейшая информация о работе сервера в вики.
