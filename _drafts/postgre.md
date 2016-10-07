
37
down vote
accepted
Modify password for role postgres:

sudo -u postgres psql postgres

alter user postgres with password 'postgres';
Now connect to pgadmin using username postgres and password postgres

Now you can create roles & databases using pgAdmin
