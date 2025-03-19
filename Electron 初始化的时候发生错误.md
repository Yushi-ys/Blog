pnpm run dev报错：Electron failed to install correctly, please delete node_modules/electron and try installing again

原因：node_modules的electron目录下缺少dist目录、path.txt等几个文件。其它的都正常。

解决办法：1. 删除electron目录（或@electron目录、bin目录下的electron文件）
         2. 不要用npm install重新安装，用npm update工具来重新安装