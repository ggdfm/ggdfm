---
layout: default
title: 各种java注解
nav_order: 3
parent: JAVA
---

#### 注解含义


##### hibernate 中的一对多注解 @OneToMany
````java
/**
 * 基表
 */
@Entity
@Table(name = "BASE")
public class Aclass{
    @Id
    @Column(name = "baseno", nullable = false)
    private Long policybaseno;
    @OneToMany(cascade = CascadeType.ALL, fetch = FetchType.LAZY)
    @JoinColumn(name = "baseno",referencedColumnName = "id")
    private List<AutoPolicyMultiAgentEO> csifAutFPolicyMultiAgentEOs;
}
//其中，@OneToMany这个注解写在'一对多'中一的一方，cascade中的CascadeType.ALL表示所有情况下都进行关联操作
//fetch表示加载模式， @JoinColumn注解的name属性关联的是你需要关联的另一张表的外键，referencedColumnName属性关联的是User实体类的里面的关联主键
@Entity
@Table(name = "AGENT")
public class AutoPolicyMultiAgentEO {
    
    @Column(name = "ID")
    @Id
    private Long id;// 主键
    @Column(name = "baseno", insertable = false, updatable = false)
    private Long policybaseno;//基表物理主键
}

````