# DB05

Made by:
- Chris Rosendorf
- Viktor Kim Christiansen
- William Pfaffe

## Introduction
Had a bunch of trouble setting this up. Ended up running the mysql server using systemctl, although that had issues aswell. If you run these commands or import the XML data, make sure you're using a console using the following connection command:

```
mysql -u root -p --local-infile
```

We tried in both Navicat and Workbench, although we could not seem to import or read any of the xml files, as sql would just quit. To do this you would have to edit the startup parameters for the mysql service, and restart the service (Easier this way hehe).

## Assignment 1
```
DELIMITER //
CREATE PROCEDURE denormalizeComments (p_postID INT)
    BEGIN
        UPDATE posts SET Comments = (SELECT JSON_ARRAYAGG(Text) FROM comments WHERE PostId = p_postID GROUP BY PostId) WHERE Id =  p_postID;
    END//
DELIMITER ;
```

## Assignment 2
```
DELIMITER //
CREATE TRIGGER addCommentToPost
    AFTER INSERT ON comments
        FOR EACH ROW
    BEGIN
        CALL denormalizeComments(NEW.PostId);
    END //
DELIMITER ;
```

## Assignment 3
```
DELIMITER //
CREATE PROCEDURE addNewComment(id INT, postId INT, score INT, postText TEXT, fkUser INT)
    BEGIN
        INSERT INTO comments(Id, PostId, Score, Text, CreationDate, UserId) value (id, postId, score, postText, NOW(), fkUser);
        CALL denormalizeComments(postId);
    END //
DELIMITER ;
```

## Assignment 4

