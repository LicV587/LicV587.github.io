---
title: Java系列(1)实现汉字转拼音
tags:
  - Java
categories:
  - Java
toc: true
date: 2019-05-24 16:29:44
---

![](https://raw.githubusercontent.com/LicV587/img/master/picgo/20190528221454.jpg)

<!-- more -->

> 最近遇到汉字转拼音放入场景，研究了下，结合网上的代码，提取出了一个工具方法，在此记录下，以备查询。

### 1. 引入jar包
Java汉字转成汉语拼音工具类，需要用到pinyin4j.jar包，笔者使用的是Maven项目，在pom.xml中引入依赖
```
<dependency>
    <groupId>com.belerweb</groupId>
    <artifactId>pinyin4j</artifactId>
    <version>2.5.0</version>
</dependency>
```

### 2. 工具类
```
package com.jikemix.study.util;

import com.jikemix.study.exception.MyException;
import net.sourceforge.pinyin4j.PinyinHelper;
import net.sourceforge.pinyin4j.format.HanyuPinyinCaseType;
import net.sourceforge.pinyin4j.format.HanyuPinyinOutputFormat;
import net.sourceforge.pinyin4j.format.HanyuPinyinToneType;
import net.sourceforge.pinyin4j.format.HanyuPinyinVCharType;
import net.sourceforge.pinyin4j.format.exception.BadHanyuPinyinOutputFormatCombination;

import java.util.Locale;


/**
 * 汉语拼音处理工具类<br>
 * 使用jar：com.belerweb:pinyin4j:2.5.0<br>
 * 设置拼音格式化：<br>
 *  1、声调格式：<br>
 *      HanyuPinyinToneType.WITH_TONE_NUMBER 用数字表示声调，例如：liu2<br>
 *      HanyuPinyinToneType.WITHOUT_TONE 无声调表示，例如：liu<br>
 *      HanyuPinyinToneType.WITH_TONE_MARK 用声调符号表示，例如：liú<br>
 *  2、设置特殊拼音 ü 的显示格式：<br>
 *      HanyuPinyinVCharType.WITH_U_AND_COLON 以U和一个冒号表示该拼音，例如：lu:<br>
 *      HanyuPinyinVCharType.WITH_V 以V表示该字符，例如：lv<br>
 *      HanyuPinyinVCharType.WITH_U_UNICODE 以ü表示<br>
 *  3、声调格式：<br>
 *      HanyuPinyinCaseType.LOWERCASE 转换后以全小写方式输出<br>
 *      HanyuPinyinCaseType.UPPERCASE 转换后以全大写方式输出<br>
 *
 *
 * @author lc
 * @date 2019/5/24 9:59
 */
public class PinyinOperator {

    private static final Boolean FIRST_LETTER_TRUE = true;
    private static final Boolean FIRST_LETTER_FALSE = false;
    private static final String REG_CHINESE = "[\\u4E00-\\u9FA5]+";
    private static final String REG_NUMBER = "[0-9]+";
    private static final String REG_LETTER = "[a-zA-Z]+";


    /**
     * 测试
     * @param args
     */
    public static void main(String[] args) {
        String str = "12a%$这是拼音测试ababa%$$#";
        System.out.println(getFirstLetterUp(str));
        System.out.println(getFirstLetterLow(str));
        System.out.println(getPinyinStrUp(str));
        System.out.println(getPinyinStrLow(str));
        System.out.println(getFirstChineseLetterUp(str));
        System.out.println(getFirstChineseLetterLow(str));
    }

    /**
     * 获取中文的拼音，首字母，大写
     * @param chineseStr
     * @return
     */
    public static String getFirstLetterUp(String chineseStr) {
        return toPinyin(chineseStr, FIRST_LETTER_TRUE, HanyuPinyinCaseType.UPPERCASE);
    }

    /**
     * 获取中文的拼音，首字母，小写
     * @param chineseStr
     * @return
     */
    public static String getFirstLetterLow(String chineseStr) {
        return toPinyin(chineseStr, FIRST_LETTER_TRUE, HanyuPinyinCaseType.LOWERCASE);
    }

    /**
     * 获取中文的拼音，全拼，大写
     * @param chineseStr
     * @return
     */
    public static String getPinyinStrUp(String chineseStr) {
        return toPinyin(chineseStr, FIRST_LETTER_FALSE, HanyuPinyinCaseType.UPPERCASE);
    }

    /**
     * 获取中文的拼音，全拼，小写
     * @param chineseStr
     * @return
     */
    public static String getPinyinStrLow(String chineseStr) {
        return toPinyin(chineseStr, FIRST_LETTER_FALSE, HanyuPinyinCaseType.LOWERCASE);
    }


    /**
     * 获取中文的拼音
     * @param chineseStr  需要获取拼音的中文字符
     * @param isFirstLetter   是否只获取首字母，true获取首字母，false获取全拼
     * @param caseType   拼音格式：大小写类型枚举
     * @return
     */
    private static String toPinyin(String chineseStr, Boolean isFirstLetter, HanyuPinyinCaseType caseType) {
        if (chineseStr==null || "".equals(chineseStr.trim())) {
            return null;
        }
        char[] charArr = chineseStr.trim().toCharArray();
        StringBuilder pinyin = new StringBuilder();
        //设置输出格式
        HanyuPinyinOutputFormat format = new HanyuPinyinOutputFormat();
        // 设置声调格式，此处为无声调格式
        format.setToneType(HanyuPinyinToneType.WITHOUT_TONE);
        // 设置特殊声调格式，例如“u:”或“v”或“ü”
        format.setVCharType(HanyuPinyinVCharType.WITH_V);
        // 设置大小写格式，此处为大写
        format.setCaseType(caseType);
        try {
            for (int i=0; i<charArr.length; i++) {
                String str = Character.toString(charArr[i]);
                //  如果字符是中文,则将中文转为汉语拼音
                if (str.matches(REG_CHINESE)) {
                    String[] pinyinStrArr = PinyinHelper.toHanyuPinyinStringArray(charArr[i], format);
                    if (pinyinStrArr != null) {
                        if(isFirstLetter){
                            // 获取拼音的首字母
                            pinyin.append(pinyinStrArr[0].charAt(0));
                        } else {
                            // 获取拼音的全拼
                            pinyin.append(pinyinStrArr[0]);
                        }
                    }
                //  如果是数字，则直接追加
                } else if (str.matches(REG_NUMBER)) {
                    pinyin.append(charArr[i]);
                //  如果是字母，则根据参数转大小写，再追加
                } else if (str.matches(REG_LETTER)) {
                    if(caseType == HanyuPinyinCaseType.UPPERCASE){
                        pinyin.append(Character.toString(charArr[i]).toUpperCase(Locale.CHINA));
                    } else {
                        pinyin.append(Character.toString(charArr[i]).toLowerCase(Locale.CHINA));
                    }
                //  如果是标点符号等，则不转换
                } else {

                }
            }
        } catch (BadHanyuPinyinOutputFormatCombination e) {
            throw new MyException(e.getMessage());
        }
        return pinyin.toString();
    }

    /**
     * 获取第一个汉字的拼音首字母，大写
     * @param chineseStr
     * @return
     */
    public static String getFirstChineseLetterUp(String chineseStr) {
        return getFirstLetter(chineseStr, HanyuPinyinCaseType.UPPERCASE);
    }

    /**
     * 获取第一个汉字的拼音首字母，小写
     * @param chineseStr
     * @return
     */
    public static String getFirstChineseLetterLow(String chineseStr) {
        return getFirstLetter(chineseStr, HanyuPinyinCaseType.LOWERCASE);
    }

    /**
     * 获取第一个汉字的拼音首字母
     * @param chineseStr  需要获取拼音的中文字符
     * @param caseType   拼音格式：大小写类型枚举
     * @return
     */
    private static String getFirstLetter(String chineseStr, HanyuPinyinCaseType caseType) {
        if (chineseStr==null || "".equals(chineseStr.trim())) {
            return null;
        }
        char[] charArr = chineseStr.trim().toCharArray();
        StringBuilder firstLetter = new StringBuilder();
        //设置输出格式
        HanyuPinyinOutputFormat format = new HanyuPinyinOutputFormat();
        // 设置声调格式，此处为无声调格式
        format.setToneType(HanyuPinyinToneType.WITHOUT_TONE);
        // 设置特殊声调格式，例如“u:”或“v”或“ü”
        format.setVCharType(HanyuPinyinVCharType.WITH_V);
        // 设置大小写格式，此处为大写
        format.setCaseType(caseType);
        try {
            for (int i=0; i<charArr.length; i++) {
                String str = Character.toString(charArr[i]);
                if (str.matches(REG_CHINESE)) {
                    //  如果字符是中文,则将中文转为汉语拼音
                    String[] pinyinStrArr = PinyinHelper.toHanyuPinyinStringArray(charArr[i], format);
                    if (pinyinStrArr != null) {
                        // 获取拼音的首字母
                        firstLetter.append(pinyinStrArr[0].charAt(0));
                        //  成功获取到第一个汉字的首字母，跳出循环
                        break;
                    }
                }
                //  如果非中文，如数字、字母、标点符号，则继续循环
            }
        } catch (BadHanyuPinyinOutputFormatCombination e) {
            throw new MyException(e.getMessage());
        }
        return firstLetter.toString();
    }


}

```

### 3. 后注
有博文说 **pinyin4j** 对多音字语句的处理不太理想。
需要使用 **com.github.stuxuhai:jpinyin:1.1.8** 这个jar，说是在pinyin4j的jar上进行了优化，可以实现汉字转换为拼音，并自动识别常用多音字，还支持简体转换为繁体，检查是否为简体，是否为繁体，是否为中文字符等功能。这个新的jar还没测试过，有时间测试下。

### 4. 参考文章
- [java获取中文的拼音以及获取中文拼音的首字母](https://blog.csdn.net/weixin_39297312/article/details/80944298)
- [JAVA实现汉字转换为拼音 pinyin4j/JPinyin](https://blog.csdn.net/moakun/article/details/80719859)
- [Java汉字转汉语拼音工具类](https://blog.csdn.net/HG_Harvey/article/details/79035828)