# Реалізація інформаційного та програмного забезпечення

## SQL-скрипт для створення на початкового наповнення бази даних

    -- MySQL Workbench Forward Engineering
    
    SET @OLD_UNIQUE_CHECKS=@@UNIQUE_CHECKS, UNIQUE_CHECKS=0;
    SET @OLD_FOREIGN_KEY_CHECKS=@@FOREIGN_KEY_CHECKS, FOREIGN_KEY_CHECKS=0;
    SET @OLD_SQL_MODE=@@SQL_MODE, SQL_MODE='ONLY_FULL_GROUP_BY,STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_ENGINE_SUBSTITUTION';
    
    -- -----------------------------------------------------
    -- Schema quiz
    -- -----------------------------------------------------
    DROP SCHEMA IF EXISTS `quiz` ;
    
    -- -----------------------------------------------------
    -- Schema quiz
    -- -----------------------------------------------------
    CREATE SCHEMA IF NOT EXISTS `quiz` DEFAULT CHARACTER SET utf8 ;
    USE `quiz` ;
    
    -- -----------------------------------------------------
    -- Table `quiz`.`Role`
    -- -----------------------------------------------------
    DROP TABLE IF EXISTS `quiz`.`Role` ;
    
    CREATE TABLE IF NOT EXISTS `quiz`.`Role` (
    `id` INT NOT NULL,
    `name` VARCHAR(32) NULL,
    PRIMARY KEY (`id`))
    ENGINE = InnoDB;
    
    
    -- -----------------------------------------------------
    -- Table `quiz`.`Permission`
    -- -----------------------------------------------------
    DROP TABLE IF EXISTS `quiz`.`Permission` ;
    
    CREATE TABLE IF NOT EXISTS `quiz`.`Permission` (
    `id` INT NOT NULL,
    `name` VARCHAR(32) NULL,
    PRIMARY KEY (`id`))
    ENGINE = InnoDB;
    
    
    -- -----------------------------------------------------
    -- Table `quiz`.`Grant`
    -- -----------------------------------------------------
    DROP TABLE IF EXISTS `quiz`.`Grant` ;
    
    CREATE TABLE IF NOT EXISTS `quiz`.`Grant` (
    `id` INT NOT NULL,
    `appointed` DATE NULL,
    `Role_id` INT NOT NULL,
    `Permission_id` INT NOT NULL,
    PRIMARY KEY (`id`, `Role_id`, `Permission_id`),
    INDEX `fk_Grant_Role1_idx` (`Role_id` ASC) VISIBLE,
    INDEX `fk_Grant_Permission1_idx` (`Permission_id` ASC) VISIBLE,
    CONSTRAINT `fk_Grant_Role1`
    FOREIGN KEY (`Role_id`)
    REFERENCES `quiz`.`Role` (`id`)
    ON DELETE NO ACTION
    ON UPDATE NO ACTION,
    CONSTRAINT `fk_Grant_Permission1`
    FOREIGN KEY (`Permission_id`)
    REFERENCES `quiz`.`Permission` (`id`)
    ON DELETE NO ACTION
    ON UPDATE NO ACTION)
    ENGINE = InnoDB;
    
    
    -- -----------------------------------------------------
    -- Table `quiz`.`User`
    -- -----------------------------------------------------
    DROP TABLE IF EXISTS `quiz`.`User` ;
    
    CREATE TABLE IF NOT EXISTS `quiz`.`User` (
    `id` INT NOT NULL AUTO_INCREMENT,
    `first_name` VARCHAR(45) NULL,
    `last_name` VARCHAR(45) NULL,
    `nick_name` VARCHAR(45) NULL,
    `email` VARCHAR(128) NULL,
    `password` VARCHAR(64) NULL,
    `Role_id` INT NOT NULL,
    PRIMARY KEY (`id`, `Role_id`),
    INDEX `fk_User_Role1_idx` (`Role_id` ASC) VISIBLE,
    UNIQUE INDEX `email_UNIQUE` (`email` ASC) VISIBLE,
    UNIQUE INDEX `nick_name_UNIQUE` (`nick_name` ASC) VISIBLE,
    CONSTRAINT `fk_User_Role1`
    FOREIGN KEY (`Role_id`)
    REFERENCES `quiz`.`Role` (`id`)
    ON DELETE NO ACTION
    ON UPDATE NO ACTION)
    ENGINE = InnoDB;
    
    
    -- -----------------------------------------------------
    -- Table `quiz`.`Survey`
    -- -----------------------------------------------------
    DROP TABLE IF EXISTS `quiz`.`Survey` ;
    
    CREATE TABLE IF NOT EXISTS `quiz`.`Survey` (
    `id` INT NOT NULL,
    `title` VARCHAR(45) NULL,
    `description` LONGTEXT NULL,
    `created` DATE NULL,
    `User_id` INT NOT NULL,
    `User_Role_id` INT NOT NULL,
    PRIMARY KEY (`id`, `User_id`, `User_Role_id`),
    INDEX `fk_Survey_User1_idx` (`User_id` ASC, `User_Role_id` ASC) VISIBLE,
    CONSTRAINT `fk_Survey_User1`
    FOREIGN KEY (`User_id` , `User_Role_id`)
    REFERENCES `quiz`.`User` (`id` , `Role_id`)
    ON DELETE NO ACTION
    ON UPDATE NO ACTION)
    ENGINE = InnoDB;
    
    
    -- -----------------------------------------------------
    -- Table `quiz`.`Question`
    -- -----------------------------------------------------
    DROP TABLE IF EXISTS `quiz`.`Question` ;
    
    CREATE TABLE IF NOT EXISTS `quiz`.`Question` (
    `id` INT NOT NULL,
    `text` LONGTEXT NULL,
    `type` VARCHAR(32) NULL,
    `Survey_id` INT NOT NULL,
    `Survey_User_id` INT NOT NULL,
    `Survey_User_Role_id` INT NOT NULL,
    PRIMARY KEY (`id`, `Survey_id`, `Survey_User_id`, `Survey_User_Role_id`),
    INDEX `fk_Question_Survey1_idx` (`Survey_id` ASC, `Survey_User_id` ASC, `Survey_User_Role_id` ASC) VISIBLE,
    CONSTRAINT `fk_Question_Survey1`
    FOREIGN KEY (`Survey_id` , `Survey_User_id` , `Survey_User_Role_id`)
    REFERENCES `quiz`.`Survey` (`id` , `User_id` , `User_Role_id`)
    ON DELETE NO ACTION
    ON UPDATE NO ACTION)
    ENGINE = InnoDB;
    
    
    -- -----------------------------------------------------
    -- Table `quiz`.`Answer`
    -- -----------------------------------------------------
    DROP TABLE IF EXISTS `quiz`.`Answer` ;
    
    CREATE TABLE IF NOT EXISTS `quiz`.`Answer` (
    `id` INT NOT NULL,
    `text` LONGTEXT NULL,
    `User_id` INT NOT NULL,
    `User_Role_id` INT NOT NULL,
    `Question_id` INT NOT NULL,
    `Question_Survey_id` INT NOT NULL,
    `Question_Survey_User_id` INT NOT NULL,
    `Question_Survey_User_Role_id` INT NOT NULL,
    PRIMARY KEY (`id`, `User_id`, `User_Role_id`, `Question_id`, `Question_Survey_id`, `Question_Survey_User_id`, `Question_Survey_User_Role_id`),
    INDEX `fk_Answer_User1_idx` (`User_id` ASC, `User_Role_id` ASC) VISIBLE,
    INDEX `fk_Answer_Question1_idx` (`Question_id` ASC, `Question_Survey_id` ASC, `Question_Survey_User_id` ASC, `Question_Survey_User_Role_id` ASC) VISIBLE,
    CONSTRAINT `fk_Answer_User1`
    FOREIGN KEY (`User_id` , `User_Role_id`)
    REFERENCES `quiz`.`User` (`id` , `Role_id`)
    ON DELETE NO ACTION
    ON UPDATE NO ACTION,
    CONSTRAINT `fk_Answer_Question1`
    FOREIGN KEY (`Question_id` , `Question_Survey_id` , `Question_Survey_User_id` , `Question_Survey_User_Role_id`)
    REFERENCES `quiz`.`Question` (`id` , `Survey_id` , `Survey_User_id` , `Survey_User_Role_id`)
    ON DELETE NO ACTION
    ON UPDATE NO ACTION)
    ENGINE = InnoDB;
    
    
    -- -----------------------------------------------------
    -- Table `quiz`.`State`
    -- -----------------------------------------------------
    DROP TABLE IF EXISTS `quiz`.`State` ;
    
    CREATE TABLE IF NOT EXISTS `quiz`.`State` (
    `id` INT NOT NULL,
    `name` VARCHAR(32) NULL,
    PRIMARY KEY (`id`))
    ENGINE = InnoDB;
    
    
    -- -----------------------------------------------------
    -- Table `quiz`.`Action`
    -- -----------------------------------------------------
    DROP TABLE IF EXISTS `quiz`.`Action` ;
    
    CREATE TABLE IF NOT EXISTS `quiz`.`Action` (
    `id` INT NOT NULL,
    `date` DATE NULL,
    `Survey_id` INT NOT NULL,
    `Survey_User_id` INT NOT NULL,
    `Survey_User_Role_id` INT NOT NULL,
    `Role_id` INT NOT NULL,
    `State_id` INT NOT NULL,
    PRIMARY KEY (`id`, `Survey_id`, `Survey_User_id`, `Survey_User_Role_id`, `Role_id`, `State_id`),
    INDEX `fk_Action_Survey1_idx` (`Survey_id` ASC, `Survey_User_id` ASC, `Survey_User_Role_id` ASC) VISIBLE,
    INDEX `fk_Action_Role1_idx` (`Role_id` ASC) VISIBLE,
    INDEX `fk_Action_State1_idx` (`State_id` ASC) VISIBLE,
    CONSTRAINT `fk_Action_Survey1`
    FOREIGN KEY (`Survey_id` , `Survey_User_id` , `Survey_User_Role_id`)
    REFERENCES `quiz`.`Survey` (`id` , `User_id` , `User_Role_id`)
    ON DELETE NO ACTION
    ON UPDATE NO ACTION,
    CONSTRAINT `fk_Action_Role1`
    FOREIGN KEY (`Role_id`)
    REFERENCES `quiz`.`Role` (`id`)
    ON DELETE NO ACTION
    ON UPDATE NO ACTION,
    CONSTRAINT `fk_Action_State1`
    FOREIGN KEY (`State_id`)
    REFERENCES `quiz`.`State` (`id`)
    ON DELETE NO ACTION
    ON UPDATE NO ACTION)
    ENGINE = InnoDB;
    
    
    SET SQL_MODE=@OLD_SQL_MODE;
    SET FOREIGN_KEY_CHECKS=@OLD_FOREIGN_KEY_CHECKS;
    SET UNIQUE_CHECKS=@OLD_UNIQUE_CHECKS;

## RESTfull сервіс для управління даними

RESTfull сервіс для управління таблиці User у базі даних 'quiz' створеної в MySQL. Цей застосунок був створений за допомогою
фреймворку Flask на мові Python.
RESTfull сервіс представляє собою базовий CRUD застосунок, тобто "Набор джентельмена", Create, Read, Update і Delete.

### Файл app.py

#### Імпорти:

    from flask import Flask, request, jsonify
    from flask_sqlalchemy import SQLAlchemy
    app = Flask(name)
    
    app = Flask(__name__)
    api = Api(app)

#### Конфігурація бази даних MySQL:

    app.config['SQLALCHEMY_DATABASE_URI'] = 'mysql://root:ggwp1233z@localhost/quiz'
    db = SQLAlchemy(app)


#### Створення моделі для Permission:

    class Permission(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    name = db.Column(db.String(32), nullable=False)

    def init(self, id, name):
        self.id = id
        self.name = name


#### Ініціалізація бази даних:

    # Маршрут для отримання списку дозволів
    @app.route('/permissions', methods=['GET'])
    def get_permissions():
    permissions = Permission.query.all()
    # Формуємо список словників, щоб передати у форматі JSON
    result = [{'id': permission.id, 'name': permission.name} for permission in permissions]
    return jsonify(result)
    
    # Маршрут для створення нового дозволу
    @app.route('/permissions', methods=['POST'])
    def create_permission():
    data = request.get_json()
    id = data.get('id')
    name = data.get('name')
    
        if not id or not name:
            return jsonify({'error': 'Ідентифікатор та ім`я обов`язкові'}), 400
    
        existing_permission = Permission.query.filter_by(id=id).first()
        if existing_permission:
            return jsonify({'error': f'Дозвіл з ідентифікатором {id} вже існує'}), 409
    
        existing_permission = Permission.query.filter_by(name=name).first()
        if existing_permission:
            return jsonify({'error': f'Дозвіл з іменем {name} вже існує'}), 409
    
        new_permission = Permission(id=id, name=name)
        db.session.add(new_permission)
        db.session.commit()
    
        return jsonify({'message': f'Дозвіл з ідентифікатором {id} та іменем {name} створений успішно'}), 201
    
    # Маршрут для оновлення існуючого дозволу
    @app.route('/permissions/<int:id>', methods=['PUT'])
    def update_permission(id):
    permission = Permission.query.get(id)
    
        if not permission:
            return jsonify({'error': f'Дозвіл з ідентифікатором {id} не знайдений'}), 404
    
        data = request.get_json()
        name = data.get('name')
    
        if not name:
            return jsonify({'error': 'Ім`я обов`язкове'}), 400
    
        existing_permission = Permission.query.filter(Permission.name == name, Permission.id != id).first()
        if existing_permission:
            return jsonify({'error': f'Дозвіл з іменем {name} вже існує'}), 409
    
        permission.name = name
        db.session.commit()
    
        return jsonify({'message': f'Дозвіл з ідентифікатором {id} оновлений успішно, нове ім`я - {name}'})
    
    # Маршрут для видалення дозволу за ідентифікатором
    @app.route('/permissions/<int:id>', methods=['DELETE'])
    def delete_permission(id):
    permission = Permission.query.get(id)
    
        if not permission:
            return jsonify({'error': f'Дозвіл з ідентифікатором {id} не знайдений'}), 404
        permission_name = permission.name
    
        db.session.delete(permission)
        db.session.commit()
        return jsonify({'message': f'Дозвіл з ідентифікатором {id} та іменем {permission_name} видалений успішно'})
    
    if name == 'main':
    app.run(debug=True)






