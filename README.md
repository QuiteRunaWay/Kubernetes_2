# Домашнее задание к занятию "12.2 Команды для работы с Kubernetes"
Кластер — это сложная система, с которой крайне редко работает один человек. Квалифицированный devops умеет наладить работу всей команды, занимающейся каким-либо сервисом.
После знакомства с кластером вас попросили выдать доступ нескольким разработчикам. Помимо этого требуется служебный аккаунт для просмотра логов.

## Задание 1: Запуск пода из образа в деплойменте
Для начала следует разобраться с прямым запуском приложений из консоли. Такой подход поможет быстро развернуть инструменты отладки в кластере. Требуется запустить деплоймент на основе образа из hello world уже через deployment. Сразу стоит запустить 2 копии приложения (replicas=2). 

Требования:
 * пример из hello world запущен в качестве deployment
 * количество реплик в deployment установлено в 2
 * наличие deployment можно проверить командой kubectl get deployment
 * наличие подов можно проверить командой kubectl get pods

###Ответ: если я правильно понимаю, то в предыдущем задании у нас сервер приложений и приложение поднималось автоматов само, а теперь нам надо 1. Создать свой Deployments 2. Поднять веб сервер (пусть будет nginx) 3. Надо поднять самостоятельно приложение Hello World в созданном Deployments 4. Надо размножить, сделать реплики, нашего приложения.

За основу для развертывания Deployments и веб-сервера буду использовать инструкции со страницы: https://kubernetes.io/docs/concepts/workloads/controllers/deployment/ 

1. Создаем Deployments для дальнейшего развертывания наших приложений и т.к. используем kubectl apply то сразу происходит разворачивание nginx из образа:
![image](https://user-images.githubusercontent.com/92969676/186331657-fbc58682-a621-4920-898f-f5acd6c838c8.png)

2. В итоге развернуто Deployments и 3 реплики nginx:

![image](https://user-images.githubusercontent.com/92969676/186331852-d38b798a-95bc-4e26-a1ce-920d9aa48a76.png)
![image](https://user-images.githubusercontent.com/92969676/186331935-f45e9c65-2177-466d-b22f-31d42e8033db.png)

3. Убедился, что пример с официального ресурса работает, далее чтобы было быстрее просто, нашел описание развертывания приложения hello world на nginx в Kubernetes (https://gist.github.com/sdenel/1bd2c8b5975393ababbcff9b57784e82) доработал файл yaml чтобы при создании у нас было сразу 2 реплики как по заданию. В итоге результат: 

Запускаем прокси до нашего созданного ресурса:
![image](https://user-images.githubusercontent.com/92969676/186579625-9bb91b52-3045-4a0d-af04-74a326b029d5.png)

Наше приложение было размещено в Namespace Default

![image](https://user-images.githubusercontent.com/92969676/186581187-ddb78b0b-4e24-4951-b0e0-d4499f3ddb9f.png)

На скриншоте у нас самые верхние поды - это то, что мы только что создали:

![image](https://user-images.githubusercontent.com/92969676/186581300-b30c0451-cb2c-4520-b17e-48f77ee68b7d.png)


![image](https://user-images.githubusercontent.com/92969676/186580985-cad96933-30c0-4025-9c94-b5d4bca1c73e.png)


![image](https://user-images.githubusercontent.com/92969676/186580903-ab811517-2d71-47d8-ae11-7eea849d7409.png)



## Задание 2: Просмотр логов для разработки
Разработчикам крайне важно получать обратную связь от штатно работающего приложения и, еще важнее, об ошибках в его работе. 
Требуется создать пользователя и выдать ему доступ на чтение конфигурации и логов подов в app-namespace.

Требования: 
 * создан новый токен доступа для пользователя
 * пользователь прописан в локальный конфиг (~/.kube/config, блок users)
 * пользователь может просматривать логи подов и их конфигурацию (kubectl logs pod <pod_id>, kubectl describe pod <pod_id>)

### Ответ: 

Перед тем, как что-то делать с сертификатами, необходимо поставить утилиту kubeadm и kubelet.

сертификат для пользователя генерим по инструкции: https://kubernetes.io/docs/reference/access-authn-authz/certificate-signing-requests/ 

## Задание 3: Изменение количества реплик 
Поработав с приложением, вы получили запрос на увеличение количества реплик приложения для нагрузки. Необходимо изменить запущенный deployment, увеличив количество реплик до 5. Посмотрите статус запущенных подов после увеличения реплик. 

Требования:
 * в deployment из задания 1 изменено количество реплик на 5
 * проверить что все поды перешли в статус running (kubectl get pods)

### Ответ: 

Изменил количество реплик в yaml файле, прокатил его повторно, количество реплик было изменено:

![image](https://user-images.githubusercontent.com/92969676/186582616-2574b7c4-7476-4527-b91b-12688e66447e.png)

По времени можно увидеть, что реплики не пересоздались, а именно добавились к предыдущим 2, которые были.
