Поднимаем стенд в Docker
```
liza@INBOOKX3Plus:~/lab_2$ docker compose up -d --build
#1 [internal] load local bake definitions
#1 reading from stdin 469B done
#1 DONE 0.0s

#2 [internal] load build definition from Dockerfile
#2 transferring dockerfile: 454B done
#2 DONE 0.0s

#3 [internal] load metadata for docker.io/library/python:3.11-slim
#3 DONE 0.3s

#4 [internal] load .dockerignore
#4 transferring context: 2B done
#4 DONE 0.0s

#5 [internal] load build context
#5 transferring context: 137B done
#5 DONE 0.0s

#6 [builder 1/4] FROM docker.io/library/python:3.11-slim@sha256:8eb5fc663972b871c528fef04be4eaa9ab8ab4539a5316c4b8c133771214a617
#6 resolve docker.io/library/python:3.11-slim@sha256:8eb5fc663972b871c528fef04be4eaa9ab8ab4539a5316c4b8c133771214a617 0.0s done
#6 DONE 0.0s

#7 [stage-1 4/5] COPY --from=builder /root/.local /home/appuser/.local
#7 CACHED

#8 [builder 2/4] WORKDIR /app
#8 CACHED

#9 [builder 4/4] RUN pip install --user --no-cache-dir -r requirements.txt
#9 CACHED

#10 [stage-1 3/5] RUN useradd -m appuser && chown -R appuser /app
#10 CACHED

#11 [builder 3/4] COPY requirements.txt .
#11 CACHED

#12 [stage-1 5/5] COPY app/ .
#12 CACHED

#13 exporting to image
#13 exporting layers done
#13 exporting manifest sha256:ce25f48687b3e4c0c0f477b38fd96c45020d8c0a82cffa0c43f72cd0ee5ccf03 done
#13 exporting config sha256:a8d6c0ad6e3132bde6da5dc6226ec87ecbcd7702331a888e526e5228f5705119 done
#13 exporting attestation manifest sha256:8edadac5bc332ef8cb4371df6339cf04ddcf84c68a0c320fcb0f8d9a086373a4 0.0s done
#13 exporting manifest list sha256:68e38d001aa48408d2cf8f90490d5afbf05b88e01dbfe713895edae323df505b
#13 exporting manifest list sha256:68e38d001aa48408d2cf8f90490d5afbf05b88e01dbfe713895edae323df505b 0.0s done
#13 naming to docker.io/library/lab_2-api:latest done
#13 unpacking to docker.io/library/lab_2-api:latest 0.0s done
#13 DONE 0.1s

#14 resolving provenance for metadata file
#14 DONE 0.0s
[+] Running 9/9
 ✔ lab_2-api                   Built                                                                               0.0s
 ✔ Network lab_2_public        Created                                                                             0.1s
 ✔ Network lab_2_backend       Created                                                                             0.0s
 ✔ Volume "lab_2_pgdata"       Created                                                                             0.0s
 ✔ Container lab_2-traefik-1   Started                                                                             1.0s
 ✔ Container lab_2-postgres-1  Healthy                                                                             6.0s
 ✔ Container lab_2-redis-1     Started                                                                             1.0s
 ✔ Container lab_2-adminer-1   Started                                                                             1.0s
 ✔ Container lab_2-api-1       Started                                                                             6.0s
```
Проверка
```
liza@INBOOKX3Plus:~/lab_2$ curl -s http://api.localhost/cache
{"cache":"ok"}liza@INBOOKX3curl -s http://api.localhost/healthz
{"status":"ok"}liza@INBOOKX3Plus:~/lab_2$ curl -s http://api.localhost/db
{"db":1}liza@INBOOKX3Plus:~/lab_2$
```
Состояние
```
liza@INBOOKX3Plus:~/lab_2$ docker compose ps
NAME               IMAGE                COMMAND                  SERVICE    CREATED         STATUS                   PORTS
lab_2-adminer-1    adminer              "entrypoint.sh docke…"   adminer    3 minutes ago   Up 3 minutes             8080/tcp
lab_2-api-1        lab_2-api            "uvicorn main:app --…"   api        3 minutes ago   Up 3 minutes
lab_2-postgres-1   postgres:16-alpine   "docker-entrypoint.s…"   postgres   3 minutes ago   Up 3 minutes (healthy)
lab_2-redis-1      redis:7-alpine       "docker-entrypoint.s…"   redis      3 minutes ago   Up 3 minutes
lab_2-traefik-1    traefik:v3.1         "/entrypoint.sh --pr…"   traefik    3 minutes ago   Up 3 minutes             0.0.0.0:80->80/tcp, [::]:80->80/tcp
```
Логи
```
liza@INBOOKX3Plus:~/lab_2$ docker compose logs -f api
api-1  | INFO:     Started server process [1]
api-1  | INFO:     Waiting for application startup.
api-1  | INFO:     Application startup complete.
api-1  | INFO:     Uvicorn running on http://0.0.0.0:8000 (Press CTRL+C to quit)
api-1  | INFO:     172.21.0.2:49046 - "GET /cache HTTP/1.1" 200 OK
api-1  | INFO:     172.21.0.2:34058 - "GET /healthz HTTP/1.1" 200 OK
api-1  | INFO:     172.21.0.2:60200 - "GET /db HTTP/1.1" 200 OK
```
