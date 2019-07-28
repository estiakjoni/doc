sudo chmod -R 755 /home/naim/www/new

sudo chown -R www-data: /home/naim/www/new/storage/

sudo chown -R www-data: /home/naim/www/new/public/

sudo cp /etc/apache2/sites-available/api.conf /etc/apache2/sites-available/new.conf

sudo nano /etc/apache2/sites-available/new.conf

sudo a2ensite new.conf
