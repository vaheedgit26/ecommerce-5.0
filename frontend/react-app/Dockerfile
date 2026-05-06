############   Stage-I (Build)  ###############################
FROM node:18-alpine as build

WORKDIR /app

# ✅ Declare build arguments
ARG USER_POOL_ID
ARG CLIENT_ID
# ARG API_BASE_URL

# ✅ Convert them to env (so React can use them)
ENV REACT_APP_USER_POOL_ID=$USER_POOL_ID
ENV REACT_APP_CLIENT_ID=$CLIENT_ID
# ENV REACT_APP_API_BASE_URL=$API_BASE_URL

COPY package*.json ./
RUN npm install

COPY . .
RUN npm run build

##################  Stage-II (Nginx)  ##############################

FROM nginx:stable-alpine

# Remove default server and default index
RUN rm -f \
    /usr/share/nginx/html/index.html \
    /etc/nginx/conf.d/default.conf

# Prepare directories for non-root nginx
RUN mkdir -p \
    /var/cache/nginx/client_temp \
    /var/cache/nginx/proxy_temp \
    /var/cache/nginx/fastcgi_temp \
    /var/cache/nginx/uwsgi_temp \
    /var/cache/nginx/scgi_temp \
    /run \
    /etc/nginx/ssl \
  && chown -R nginx:nginx \
    /var/cache/nginx \
    /var/log/nginx \
    /etc/nginx \
    /run \
  && chmod 755 /etc/nginx
  
# COPY BUILD output
COPY --from=build /app/build /usr/share/nginx/html

# Copy custom NGINX config
#COPY ecommerce.conf /etc/nginx/conf.d/ecommerce.conf

# Ensure config file permissions
RUN chmod 644 /etc/nginx/conf.d/*.conf

EXPOSE 8080

USER nginx

CMD ["nginx", "-g", "daemon off;"]
