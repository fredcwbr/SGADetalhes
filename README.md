# SGADetalhes
SGA-Detalhes
Detalhes sobre o processo de instalacao / configuracao do SGA 2.2.0 

Em construcao ., 


Apenas os apontamentos para utilizar o MERCURE (por enquanto ) 


root@Fila:/etc/supervisor/conf.d# cat mercurehub.conf 
```
[program:mercurehub]
environment            = HOME=/var/www/mercure,ADDR=<OIPPARABINDDOSERVICOMERCURE>:3000,MERCURE_SUBSCRIBER_JWT_KEY='!ChangeMe!',MERCURE_PUBLISHER_JWT_KEY='!ChangeMe!',ALLOW_ANONYMOUS=1,CORS_ALLOWED_ORIGIN='[http://*:3000]',DEBUG=1 
command                 = bash -c "ulimit -n 10000; cd /var/www/mercure; exec ./mercure run -config Caddyfile.dev"
process_name            = mercurehub
numprocs                = 1
autostart               = true
autorestart             = true
user                    = www-data
stdout_logfile          = /var/log/supervisord/logs/mercurehub_info.log
stdout_logfile_maxbytes = 1MB
stderr_logfile          = /var/log/supervisord/logs/mercurehub_error.log
stderr_logfile_maxbytes = 1MB
```
no   www/mercure ... Caddyfile.dev (o mesmo que configurou no supervisor acima)   .... .vvvvvvvvv
```
# Learn how to configure the Mercure.rocks Hub on https://mercure.rocks/docs/hub/config
{
	{$GLOBAL_OPTIONS}
 	auto_https off
        http_port 3000
        https_port 3443
}
http://localhost:3000, http://<NOMEDOSITEMERCURE>:3000, http://<SETIVERMAISNOMESDOSITEMERCURE>:3000
log
route {
	encode zstd gzip
	mercure {
		# Transport to use (default to Bolt)
		transport_url {$MERCURE_TRANSPORT_URL:bolt://mercure.db}
		# Publisher JWT key
		publisher_jwt {env.MERCURE_PUBLISHER_JWT_KEY} {env.MERCURE_PUBLISHER_JWT_ALG}
		# Subscriber JWT key
		subscriber_jwt {env.MERCURE_SUBSCRIBER_JWT_KEY} {env.MERCURE_SUBSCRIBER_JWT_ALG}
		# Extra directives
		{$MERCURE_EXTRA_DIRECTIVES}
	}
	respond /healthz 200
	respond "Not Found" 404
}
```

.env no SGA-220
```
# CORS_ALLOW_ORIGIN=^https?://localhost(:[0-9]+)?$
CORS_ALLOW_ORIGIN=^https?://.*\.<NOMEDOSERVIDORMERCUREAQUI>goiasfomento\.com:3000$
###< nelmio/cors-bundle ###


MERCURE_PUBLISH_URL=http://<NOMEDOSERVIDORMERCUREAQUI>:3000/.well-known/mercure
# The default token is signed with the secret key: !ChangeMe!
MERCURE_JWT_TOKEN=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJtZXJjdXJlIjp7InB1Ymxpc2giOltdfX0.Oo0yg7y4yMa1vr_bziltxuTCqb8JVHKxp-f_FwwOim0
# Novo SGA custom var
MERCURE_CONSUMER_URL=http://<NOMEDOSERVIDORMERCUREAQUI>:3000/.well-known/mercure
###< symfony/mercure-bundle ###
```
