FROM nginx:1.30.5

EXPOSE 8080

ENV CATALOGUE_HOST=catalogue \
    USER_HOST=user \
    CART_HOST=cart \
    SHIPPING_HOST=shipping \
    PAYMENT_HOST=payment \
    RATINGS_HOST=ratings 

RUN apt-get update \
    && apt-get install -y --no-install-recommends bash curl \
    && rm -rf /var/lib/apt/lists/*

COPY --chmod=755 entrypoint.sh /root/entrypoint.sh
ENTRYPOINT ["/root/entrypoint.sh"]

COPY default.conf.template /etc/nginx/conf.d/default.conf.template
COPY static /usr/share/nginx/html
