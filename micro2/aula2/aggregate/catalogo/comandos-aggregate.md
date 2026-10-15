docker-compose up -d db-servicos

docker-compose up -d localstack

docker-compose exec localstack bash

# Criando queue DLQ dentro do localstack exec
awslocal --endpoint-url=http://localhost:4566 sqs create-queue --queue-name produto-atualizacao-dlq --region us-east-1

# Criando queue dentro do localstack exec
awslocal --endpoint-url=http://localhost:4566 sqs create-queue --queue-name produto-atualizacao --region us-east-1 --attributes '{"RedrivePolicy":"{\"deadLetterTargetArn\":\"arn:aws:sqs:us-east-1:000000000000:produto-atualizacao-dlq\",\"maxReceiveCount\":3}"}'

# Criando usuário para conectar na queue
awslocal iam create-user --user-name catalogo-user

# Criar access key e secret access key, a colocar essas keys no main.py do domain-model
awslocal iam create-access-key --user-name catalogo-user

# Build do 
docker-compose build catalogo-aggregate

docker-compose up -d catalogo-aggregate