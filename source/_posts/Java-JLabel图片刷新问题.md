---
title: Java JLabel图片刷新问题
tags: [java, jLabel]
categories: [编程, bug]
date: 2020-06-16
---

jLabel 设置了图片，当图片文件改变时，展示图片的内容却没有改变

<!--more-->

直接用以下方法无法刷新：

```js
JPanel.removeAll();
JPanel.repaint();
ImageIcon image = new ImageIcon(path);
JLabel.setIcon(image);
JPanel.add(JLabel);
JPanel.validate();
label.setIcon(null);
```

这个方法也不行

```js
JLabel.setIcon(new ImageIcon(path));
JLabel.repaint();
JLabel.updateUI();
JLabel.setVisible(true);
```

**解决方案**

```js
ImageIcon image = new ImageIcon(ImageIO.read(new File(path)));
JLabel.setIcon(image);
```

这样子，当图片文件改变时，展示时 JLable 的图片展示也能随着刷新。
