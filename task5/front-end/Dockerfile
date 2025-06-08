FROM nginx:latest
# Copy the HTML file from the root directory
COPY index.html /var/www/html/softy-pinko-front-end/
# Copy all assets (CSS, JS, images) recursively
COPY assets/ /var/www/html/softy-pinko-front-end/assets/
# Copy NGINX configuration
COPY ./softy-pinko-front-end.conf /etc/nginx/conf.d/default.conf
