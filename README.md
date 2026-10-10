cd ~/10-10 && cat > README.md <<'EOF'
# Домашнее задание «Введение в Terraform»

## Ответы

### 1. terraform --version
![1](1.jpg)

Terraform v1.16.5 (>= 1.12.0).

### 2. Секрет в state-файле
![2](2.jpg)

В terraform.tfstate секретное содержимое ресурса random_password хранится в атрибуте result.
- Ключ: result
- Значение: CZRgsInF3i6hvgBt

### 3. Ошибки terraform validate
![3](3.jpg)

1. У docker_image не было имени — исправлено на resource "docker_image" "nginx".
2. Имя 1nginx начинается с цифры — переименовано в nginx.
3. Ссылка на несуществующий ресурс — исправлено на random_password.random_string.result.

### 4. terraform destroy
![4](4.jpg)

После destroy state-файл:
{
  "version": 4,
  "terraform_version": "1.16.5",
  "serial": 11,
  "lineage": "d288a01c-f682-aa60-7717-44f17f7f6bd6",
  "outputs": {},
  "resources": [],
  "check_results": null
}

### 5. docker ps с hello_world
![5](5.jpg)

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

### 6. Про -auto-approve
![6](6.jpg)

Ключ -auto-approve пропускает подтверждение yes. Опасность: изменения применяются без проверки человеком. Полезен в CI/CD и скриптах.

### 7. Почему образ nginx:latest не был удалён
![7](7.jpg)

В коде установлен keep_locally = true.

Документация провайдера docker, ресурс docker_image:
keep_locally (Boolean) If true, then the Docker image won't be deleted on destroy operation.
EOF
