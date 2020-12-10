
压缩:  tar czf qinzi-h5.tar.gz qinzi-h5

解压:  tar xzvf qinzi-h5.tar.gz -C ./temp


pm2 start yarn --name qinzi-h5 -- run start-prod 