# Ответы

## 1. Версия Terraform
Terraform v1.16.5 (>= 1.12.0).

## 2. Где хранить секреты
Согласно .gitignore — в файле `terraform.tfvars` (и вообще в `*.tfvars`).

## 3. Секрет в state
Ключ: `result`
Значение: `CZRgsInF3i6hvgBt`

## 4. Ошибки в main.tf
1. У `docker_image` не было имени — исправлено на `resource "docker_image" "nginx"`.
2. Имя `1nginx` начинается с цифры — переименовано в `nginx`.
3. Ссылка на несуществующий ресурс — исправлено на `random_password.random_string.result`.

## 5. Исправленный фрагмент
```hcl
resource "docker_image" "nginx" {
  name         = "nginx:latest"
  keep_locally = true
}
resource "docker_container" "nginx" {
  image = docker_image.nginx.image_id
  name  = "hello_world"
  ports {
    internal = 80
    external = 9090
  }
}
```

## 6. Про -auto-approve
Пропускает подтверждение yes. Опасность: изменения применяются без проверки человеком. Полезен в CI/CD и скриптах.

## 7. state после destroy
```json
{
  "version": 4,
  "terraform_version": "1.16.5",
  "serial": 11,
  "lineage": "d288a01c-f682-aa60-7717-44f17f7f6bd6",
  "outputs": {},
  "resources": [],
  "check_results": null
}
```

## 8. Почему образ nginx:latest не удалён
В коде установлен `keep_locally = true`.
Документация провайдера docker, ресурс `docker_image`:
> **keep_locally** (Boolean) If true, then the Docker image will not be deleted on destroy operation.
