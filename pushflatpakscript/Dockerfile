FROM python:3.9.7

RUN groupadd --gid 10001 app && \
    useradd -g app --uid 10001 --shell /usr/sbin/nologin --create-home --home-dir /app app

RUN ln -s /app/docker.d/healthcheck /bin/healthcheck

WORKDIR /app
COPY . /app

RUN /app/docker.d/image_setup.sh

USER app

RUN python -m venv /app \
 && cd pushflatpakscript \
 && /app/bin/pip install --no-deps -r requirements/base.txt \
 && /app/bin/pip install . \
 && python -m venv /app/configloader_venv \
 && cd /app/configloader \
 && /app/configloader_venv/bin/pip install --no-deps -r requirements/base.txt \
 && /app/configloader_venv/bin/pip install . \
 && python -m venv /app/flat_manager_venv \
 && /app/flat_manager_venv/bin/pip install --no-deps -r /app/pushflatpakscript/requirements/flat-manager.txt \
 && curl -Ls \
    https://github.com/flatpak/flat-manager/raw/d9d7972b24de6022c7079f8721fd8335c3319dc5/flat-manager-client | \
    sed -e '1i#!/app/flat_manager_venv/bin/python' -e '1d' > /app/flat_manager_venv/bin/flat-manager-client \
 && chmod 755 /app/flat_manager_venv/bin/flat-manager-client \
 && echo "5024c853d7529e82fc4a9fb9b982e561598df033545187238c9be8c791a6797c /app/flat_manager_venv/bin/flat-manager-client" | sha256sum -c \
 && cd /app


CMD ["/app/docker.d/init.sh"]
