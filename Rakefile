task :env do
  sh 'gcloud container clusters get-credentials dev-cluster'
end

task secrets: %i(env) do
  sh 'kubectl apply -f 01-secrets.yml'
end

namespace 'postgres' do
  task :disk do
    sh 'gcloud compute disks describe postgres' do |ok, _res|
      if ok
        puts 'Disk already exists'
      else
        sh 'gcloud compute disks create --size=20GB postgres'
      end
    end
  end

  task service: %i(env) do
    sh 'kubectl apply -f 04-postgres-service.yml'
  end

  task deployment: %i(env secrets) do
    sh 'kubectl apply -f 05-postgres-deployment.yml'
  end
end
task postgres: %w(postgres:disk postgres:service postgres:deployment)

namespace 'cog' do
  task service: %i(env) do
    sh 'kubectl apply -f 06-cog-service.yml'
  end

  task deployment: %i(env secrets) do
    sh 'kubectl apply -f 07-cog-deployment.yml'
  end
end
task cog: %w(cog:service cog:deployment)

namespace 'relay' do
  task deployment: %i(env secrets) do
    sh 'kubectl apply -f 08-relay-deployment.yml'
  end
end
task relay: %w(relay:deployment)

# This probably won't work since Postgres takes a second on the intial start
# Instead run each namespace individually
task all: %w(env secrets postgres cog relay)
