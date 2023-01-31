1. Створюємо власний docker image для збірки та деплою front-end частини проекту в S3. В нашому випадку це Dockerfile.aws, що знаходиться в project-frontend.

2. Створюємо наш Bucket в AWS, в який в подальшому ми будемо деплоїти наш frontend.

3. Створюємо користувача (create user) через iam AWS та у вкладці Security credentials створюємо ключі (create access keys), які ми використаємо під час запуску основної команди (пункт 6).

4. Для надання доступу до нашого Bucket необхідно створити Policy в самому Bucket у вкладці Permissions з правилом Allow до нашого S3 з вказанням користувача якого ми створили.

{
    "Version": "2012-10-17",
    "Id": "ExamplePolicy01",
    "Statement": [
        {
            "Sid": "ExampleStatement01",
            "Effect": "Allow",
            "Principal": {
                "AWS": "arn:aws:iam::123456789:user/user_name"
            },
            "Action": [
                "s3:*Object",
                "s3:ListBucket"
            ],
            "Resource": [
                "arn:aws:s3:::conduit-fe-ithillel/*",
                "arn:aws:s3:::conduit-fe-ithillel"
            ]
        }
    ]
}

Налаштування, що необхідно було виконати в AWS, завершені.

5. Запускаємо команду для створення нашого образу з директорії, в якій лежить наш Dockerfile.aws:

docker build -t fe-builder -f Dockerfile.aws

6. Запускаємо команду для деплою front-end частини проекту в S3:

docker run -it -v "ts-redux-react-realworld-example-app":"/app" -e AWS_ACCESS_KEY_ID='key_id' -e AWS_SECRET_ACCESS_KEY='key_id' -e AWS_DEFAULT_REGION='us-east-1' -w "/app" fe-builder /bin/bash -c 'npm clean-install && npm run build && aws s3 sync ./build/ s3://backet_name'

key_id - ключі (access keys), які ми створювали в пункті 3.

backet_name - ім'я нашого Bucket, створеного в пункті 2.
