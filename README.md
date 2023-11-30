from flask_migrate import Migrate
from flask_migrate import MigrateCommand
from flask_script import Manager

migrate = Migrate(app, db)
manager.add_command('db', MigrateCommand)
