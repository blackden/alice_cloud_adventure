# 📌 Домашнее задание для слушателей после урока “Алиса в мире облаков”

🎯 **Цель**: закрепить навыки работы с Terraform и Ansible, научиться развертывать и настраивать облачную инфраструктуру самостоятельно.

---

## 🔹 Часть 1: Terraform – создание инфраструктуры

📌 **Задание**:

  1. Разверните виртуальный сервер в облаке с помощью Terraform
  2. Настройте переменные (variables.tf) для удобства управления параметрами.
  3. Выведите в outputs.tf IP-адрес созданного сервера

📜 **Пример структуры файлов Terraform**:

```sh
terraform_project/
│── main.tf         # Основной код Terraform
│── variables.tf    # Переменные
│── outputs.tf      # Вывод IP-адресов
│── terraform.tfvars # Значения переменных
```

💡 **Дополнительный вопрос**:

- Что произойдет, если вручную удалить сервер, созданный Terraform? Как Terraform узнает об изменении?

---

## 🔹 Часть 2: Ansible – настройка сервера

📌 **Задание**:

1. Подключитесь к созданному серверу через Ansible.
2. Настройте установку Nginx из официального репозитория с помощью ansible-playbook, используя официальную документацию Nginx.
3. Проверьте, работает ли веб-сервер (curl <http://your-server-ip>).

📜 **Пример структуры файлов Ansible**:

```sh
ansible_project/
│── inventory.ini   # Файл с серверами
│── install_nginx.yml # Playbook для установки Nginx
```

💡 **Дополнительный вопрос**:

- Как можно сделать так, чтобы Ansible мог работать с несколькими серверами сразу?

---

## 🔹 Часть 3: Финальное задание (бонус)

🔹 **Автоматизируйте процесс**:

- После terraform apply Ansible должен автоматически запускаться и настраивать сервер
- Для этого используйте Terraform provisioner или Ansible pull

---

## 📌 Как сдавать задание?

- Выложите код на GitHub
- Опишите в README.md
- Добавьте скриншоты работы Terraform и Ansible

👉 **Кто выполнит бонусное задание – получит +1 к карме!**

---
---

## 📌 Продвинутые задания по Terraform и Ansible

🎯 **Цель**: дать сложные, но полезные задачи для продвинутых слушателей, которые хотят глубже погрузиться в автоматизацию DevOps.

---

## 🔹 Задание 1: Динамическое масштабирование через Terraform

💡 **Идея**: Использовать Terraform-модули и переменные для автоматического создания N серверов вместо одного.

📌 **Что нужно сделать**?

1. Создать Terraform-модуль для сервера
2. Использовать переменную count, чтобы Terraform мог создавать несколько серверов одной командой.
3. Вывести в outputs.tf все IP-адреса созданных машин.

📜 **Пример main.tf**:

```hcl
module "web_servers" {
  source     = "./modules/server"
  count      = 3
  name       = "web-${count.index}"
  image_id   = var.image_id
  preset_id  = var.preset_id
  project_id = var.project_id
}
```

✅ **Ожидаемый результат**:

После terraform apply будет создано 3 сервера с уникальными именами и IP-адресами.

🚀 **Дополнительное усложнение**:

- Настроить балансировщик нагрузки, чтобы автоматически распределять трафик между этими серверами.

---

## 🔹 Задание 2: Автоматизация деплоя Docker-контейнеров с Ansible

💡 **Идея**: Настроить автоматическую установку Docker + запуск контейнера на созданном сервере.

📌 **Что нужно сделать**?

1. Написать Ansible playbook для установки Docker из официального репозитория Docker.
2. Запустить Nginx в контейнере, используя Ansible.
3. Сделать это параметризуемым: чтобы можно было менять образ контейнера через переменную.

📜 **Пример docker_setup.yml**

```yaml
- name: Установить Docker и запустить контейнер
  hosts: webservers
  become: true
  tasks:
    - name: Установить Docker
      apt:
        name: docker.io
        state: present

    - name: Запустить Nginx-контейнер
      docker_container:
        name: nginx
        image: nginx:latest
        state: started
        ports:
          - "80:80"
```

✅ **Ожидаемый результат**:

После ansible-playbook docker_setup.yml на сервере должен работать Nginx в контейнере.

🚀 **Дополнительное усложнение**:

- Развернуть Docker Compose через Ansible.
- Запустить свой образ приложения вместо Nginx.

---

## 🔹 Задание 3: CI/CD для Kubernetes с Terraform и Ansible

💡 **Идея**: Использовать GitHub Actions (или GitLab CI/CD) для автоматического развертывания инфраструктуры и деплоя приложения в Kubernetes.

📌 **Что нужно сделать?**

1. Создать Terraform-код для развертывания K8s-кластера (можно через k3s или GKE/EKS/AKS).
2. Написать Ansible playbook, который автоматически деплоит приложение в кластер.
3. Настроить CI/CD-пайплайн, который запускает Terraform и Ansible после коммита в репозиторий.

📜 **Пример .github/workflows/deploy.yml (GitHub Actions)**

```yaml
name: Deploy to Kubernetes

on:
  push:
    branches:
      - master

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout репозитория
        uses: actions/checkout@v3

      - name: Установка Terraform
        run: sudo apt-get install -y terraform

      - name: Запуск Terraform
        run: terraform apply -auto-approve

      - name: Запуск Ansible
        run: ansible-playbook -i inventory.ini deploy_app.yml
```

✅ **Ожидаемый результат**:

При каждом коммите в master Terraform создаёт кластер, а Ansible деплоит приложение в него.

🚀 **Дополнительное усложнение**:

- Использовать Helm Charts вместо обычных манифестов.
- Добавить автоматическое масштабирование через K8s HPA.
