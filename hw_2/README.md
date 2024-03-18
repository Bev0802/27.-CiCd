## Урок 2. Continuous integration (непрерывная интеграция)

<h3><span style="color: #C8A2C8;">Домашнее задание</span></h3>

1. Переписать test stage для тестирования docker-а. Достаточно проверить, что docker контейнер на базе нашего собранного образа в предыдущей job запускается.

2. Прислать ссылку на gitlab_ci.yaml либо скриншот gitlab_ci.yaml test stage и скриншот успешно отработанной job-ы test.

<h3><span style="color: #C8A2C8;">Выполнила:</span></h3>

https://gitlab.com/bev0802/cicd_01/-/tree/CI_CD_02?ref_type=heads

1. Создала вету CI_CD_02

![Создала вету CI_CD_02](gitlabHW_2.JPG)

2. Созадала Dokerfile:

```Dokerfile

FROM python:3.9-slim-buster

RUN apt-get update && apt-get install -y texlive

COPY mycv.

```

3. Доработала .gitlab-ci.yml

```.gitlab-ci.yml
# Добавление переменной
variables:
    BUILD_IMAGE: $CI_REGISTRY_IMAGE:${CI_COMMIT_REF_SLUG}

# Определение стадий сборки, тестирования и развертывания
stages:
    - build
    - test
    - deploy
    - pages
    - pdf

# Здесь $CI_REGISTRY_IMAGE — адрес репозитория проекта. $CI_COMMIT_REF_NAME — имя ветки или тег. Поскольку ветка может содержать слеши («/», например feature/my-feature), более безопасно использовать переменную $CI_COMMIT_REF_SLUG для image tag, так как Image tags не может содержать прямых слешей.
# build:
# Official docker image.
#    image: docker:latest
#    stage: build
#    services:
#        - docker:dind
#    variables:
#        BUILD_IMAGE: $CI_REGISTRY_IMAGE:${CI_COMMIT_REF_SLUG}
#    script:
#        - docker login -u bev0802 -p gldt-mctiB9cwhUzrzLG5igQK $CI_REGISTRY
#        - docker build -t $BUILD_IMAGE .
#        - docker push $BUILD_IMAGE

# Определение задачи сборки для проекта A
build_a:
    stage: build # Указание стадии сборки
    script: # Определение скрипта сборки
        - echo "This job builds something." # Вывод сообщения в консоль

# Определение задачи сборки для проекта B
build_b:
    stage: build # Указание стадии сборки
    script: # Определение скрипта сборки
        - echo "This job builds something else."  # Вывод сообщения в консоль

# Добавление pages (страницы сайта)
# С помощью artifacts:name можно задавать имя для архива артефактов (имя архива при скачивании)
pages:
    stage: pages
    script:
    - echo 'dream house'
    artifacts:
        name: "$CI_JOB_NAME"
        paths:
        - public
    only:
        - main

# artifacts:expire_in Также можно задавать время хранения артефактов с помощью expire_in
pdf:
    image: python:3.9-slim-buster
    stage: pdf
    before_script:
        - apt-get update && apt-get install -y texlive texlive-xetex texlive-luatex
    script: xelatex mycv.tex
    artifacts:
        paths:
            - mycv.pdf
        expire_in: 6 days

# Определение задачи тестирования для проекта A
test_a:
    stage: test # Указание стадии тестирования
    script: # Определение скрипта тестирования
        - echo "This job tests something. It will only run when all jobs in the"
        - echo "build stage are complete." # Вывод сообщения в консоль
    # Чтобы job test работала на всех изменениях в ветке, за исключением веток, имя которых начинается на docs, нужно использовать конструкцию
    only: [merge_requests]
    except:
        variables:
            - $CI_COMMIT_REF_NAME =~ /^docs-/

# Определение задачи тестирования для проекта B
test_b:
    stage: test # Указание стадии тестирования
    script: # Определение скрипта тестирования
        - echo "This job tests something else. It will only run when all jobs in the"
        - echo "build stage are complete too. It will start at about the same time as test_a." # Вывод сообщения в консоль

# Определение задачи развертывания для проекта A
deploy_a:
    stage: deploy # Указание стадии развертывания
    script: # Определение скрипта развертывания
        - echo "This job deploys something. It will only run when all jobs in the"
        - echo "test stage complete." # Вывод сообщения в консоль

# Определение задачи развертывания для проекта B
deploy_b:
    stage: deploy
    script:
        - echo "This job deploys something else. It willonly run when all jobs in the"
        - echo "test stage complete. It will start atabout the same time as deploy_a."
```

4. PIPELINE
   ![PIPELINE](pipeline.JPG)

5. ЖУРНАЛ PIPELINE
   ![ЖУРНАЛ PIPELINE](<Журанл pipelines.JPG>)
