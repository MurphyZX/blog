---
title: json 字典解析
date: 2025-04-16 15:04:34
tags:
  - 开发
---

```java
	/**
     * 根据字典JSON和键值获取显示名称
     *
     * @param dictJson      JSON格式的字典数据
     * @param key           键值
     * @param keyField      键字段名
     * @param labelField    标签字段名
     * @param childrenField 子节点字段名
     * @return 显示名称
     */
    private String getDictLabelByJson(String dictJson, String key, String keyField, String labelField, String childrenField) {
        if (StringUtils.isEmpty(dictJson) || StringUtils.isEmpty(key)) {
            return key;
        }
        try {
            List<Map<String, Object>> dictList = (List<Map<String, Object>>) JSONArray.parse(dictJson);
            return findLabelByKey(dictList, key, keyField, labelField, childrenField);
        } catch (Exception e) {
            log.error("解析字典JSON失败", e);
            return key;
        }
    }

    /**
     * 递归查找字典标签
     *
     * @param dictList      字典列表
     * @param key           键值
     * @param keyField      键字段名
     * @param labelField    标签字段名
     * @param childrenField 子节点字段名
     * @return 显示名称
     */
    private String findLabelByKey(List<Map<String, Object>> dictList, String key, String keyField, String labelField, String childrenField) {
        if (CollUtil.isEmpty(dictList)) {
            return key;
        }
        for (Map<String, Object> dict : dictList) {
            String dictKey = (String) dict.get(keyField);
            if (key.equals(dictKey)) {
                return (String) dict.get(labelField);
            }
            // 递归查找子节点
            if (StringUtils.isNotEmpty(childrenField)) {
                @SuppressWarnings("unchecked")
                List<Map<String, Object>> children = (List<Map<String, Object>>) dict.get(childrenField);
                if (CollUtil.isNotEmpty(children)) {
                    String childLabel = findLabelByKey(children, key, keyField, labelField, childrenField);
                    if (!childLabel.equals(key)) {
                        return childLabel;
                    }
                }
            }
        }
        return key;
    }
```

