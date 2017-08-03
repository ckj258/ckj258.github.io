---
layout: post
title: "麻将类胡牌算法"
date: 2017年8月3日17:34:13
comments: true
reward: false
tags: 
	- mahjongsever
---

第一次做麻将服务端，记录下相关算法

<!-- more -->


## 胡牌算法

```  java
/*
 *理牌
*/
	public static int[] LayCards(List<Poker> cardList)
	{
		int nCardsLay[] = new int[MJ_LAYOUT_NUM];
    	for (Poker p : cardList) {
			nCardsLay[p.getCode()]++;
		}
    	return nCardsLay;
	}
```

```  java
/*
 *验证输入已经理好的牌型是否满足 3*N规则
*/
public static boolean fitThreePairsForLayCards(int laycards[])
	{
		int i = 0;
		for (;i < MJ_LAYOUT_NUM; i++) {
			if (laycards[i]>0) {
				break;
			}
		}
		if(i==MJ_LAYOUT_NUM) return true;
		
		for(i=0;i<MJ_LAYOUT_NUM;i++)
		{
			if (laycards[i]==0)
				continue;
			else if (laycards[i]==3) {
				laycards[i] =0;
				return fitThreePairsForLayCards(laycards);
			}
			else if (i<28&&laycards[i]>0&&laycards[i+1]>0&&laycards[i+2]>0) {
				laycards[i]--;
				laycards[i+1]--;
				laycards[i+2]--;
				return fitThreePairsForLayCards(laycards);
			}
			else
			{
				return false;
			}
		}
		return true;
	}
```

```  java
/*
 *验证输入未理过的牌型是否满足 3*N规则(区分花色)  
*/
    public static boolean fitThreePairs(List<Poker> cardList)
    {
    	if (cardList.isEmpty()) {
			return true;
		}
        
    	Map<Suit,List<Poker>> suit_pokers = new HashMap<Suit, List<Poker>>();
		suit_pokers.put(Suit.TIAO,  new ArrayList<Poker>());
		suit_pokers.put(Suit.TONG,  new ArrayList<Poker>());
		suit_pokers.put(Suit.WAN,  new ArrayList<Poker>());
		suit_pokers.put(Suit.ZI,  new ArrayList<Poker>());
		for (Poker var:cardList) {
			suit_pokers.get(var.getSuit()).add(var);
		}
		for ( Suit suit:suit_pokers.keySet()) {
			int[] laycards = LayCards(suit_pokers.get(suit));
			if (!fitThreePairsForLayCards(laycards)) {
				return false;
			}
		}
		return true;
    

    }
```

```  java
/*
 *胡牌算法入口函数
 *1.找出牌型中所有两张以上的牌型并去除,作为将
 *2.验证剩下牌型是否满足 3*N 规则
*/
	public static boolean analyzeNormalHu(List<Poker> cards) {
		if (cards.size()%3!=2) {
			return false; 
		}
		List<Poker> plist = new ArrayList<Poker>();
		plist.addAll(cards);
		Collections.sort(plist, comparator);
		for (Poker p:plist) {
			if (Collections.frequency(plist, p) >= 2) {
				List<Poker> wipe2jiangplist = new ArrayList<Poker>(plist);
				wipe2jiangplist.remove(p);
				wipe2jiangplist.remove(p);
				if (fitThreePairs(wipe2jiangplist)) {
					return true;
				}
			}
		}
		return false;
	}
```






