【每日prompt】
langchain的默认RetrievalQA是如何实现？
- 用问题和store做相似匹配召回作为语境
- 组装RetrievalQA prompt使用load_qa_chain处理
- load_qa_chain可使用stuff / map_reduce / map_rank / refine
- map_reduce / map_rank为了效果做了一些few shot最慢且费token

下面是各个模式使用默认prompt模版。

-----stuff prompt----
使用以下内容来回答最后的问题。如果你不知道答案，就说你不知道，不要试图编造一个答案。
{语境}
问题： {问题}
有帮助的答案：

-----map_reduce  question prompt----
使用长文件的以下部分，看看是否有任何文本与回答问题有关。
逐字逐句地返回任何相关文本。

-----map_reduce  combine_prompt----
给出以下长篇文件的摘录部分和一个问题，创造一个最终答案。
如果你不知道答案，就说你不知道。不要试图编造一个答案。
QUESTION: Which state/country's law governs the interpretation of the contract?
=========
Content: This Agreement is governed by English law and the parties submit to the exclusive jurisdiction of the English courts in  relation to any dispute (contractual or non-contractual) concerning this Agreement save that either party may apply to any court for an  injunction or other relief to protect its Intellectual Property Rights.

Content: No Waiver. Failure or delay in exercising any right or remedy under this Agreement shall not constitute a waiver of such (or any other)  right or remedy.\n\n11.7 Severability. The invalidity, illegality or unenforceability of any term (or part of a term) of this Agreement shall not affect the continuation  in force of the remainder of the term (if any) and this Agreement.\n\n11.8 No Agency. Except as expressly stated otherwise, nothing in this Agreement shall create an agency, partnership or joint venture of any  kind between the parties.\n\n11.9 No Third-Party Beneficiaries.

Content: (b) if Google believes, in good faith, that the Distributor has violated or caused Google to violate any Anti-Bribery Laws (as  defined in Clause 8.5) or that such a violation is reasonably likely to occur,
=========
FINAL ANSWER: This Agreement is governed by English law.

QUESTION: What did the president say about Michael Jackson?
=========
Content: Madam Speaker, Madam Vice President, our First Lady and Second Gentleman. Members of Congress and the Cabinet. Justices of the Supreme Court. My fellow Americans.  \n\nLast year COVID-19 kept us apart. This year we are finally together again. \n\nTonight, we meet as Democrats Republicans and Independents. But most importantly as Americans. \n\nWith a duty to one another to the American people to the Constitution. \n\nAnd with an unwavering resolve that freedom will always triumph over tyranny. \n\nSix days ago, Russia’s Vladimir Putin sought to shake the foundations of the free world thinking he could make it bend to his menacing ways. But he badly miscalculated. \n\nHe thought he could roll into Ukraine and the world would roll over. Instead he met a wall of strength he never imagined. \n\nHe met the Ukrainian people. \n\nFrom President Zelenskyy to every Ukrainian, their fearlessness, their courage, their determination, inspires the world. \n\nGroups of citizens blocking tanks with their bodies. Everyone from students to retirees teachers turned soldiers defending their homeland.

Content: And we won’t stop. \n\nWe have lost so much to COVID-19. Time with one another. And worst of all, so much loss of life. \n\nLet’s use this moment to reset. Let’s stop looking at COVID-19 as a partisan dividing line and see it for what it is: A God-awful disease.  \n\nLet’s stop seeing each other as enemies, and start seeing each other for who we really are: Fellow Americans.  \n\nWe can’t change how divided we’ve been. But we can change how we move forward—on COVID-19 and other issues we must face together. \n\nI recently visited the New York City Police Department days after the funerals of Officer Wilbert Mora and his partner, Officer Jason Rivera. \n\nThey were responding to a 9-1-1 call when a man shot and killed them with a stolen gun. \n\nOfficer Mora was 27 years old. \n\nOfficer Rivera was 22. \n\nBoth Dominican Americans who’d grown up on the same streets they later chose to patrol as police officers. \n\nI spoke with their families and told them that we are forever in debt for their sacrifice, and we will carry on their mission to restore the trust and safety every community deserves.

Content: And a proud Ukrainian people, who have known 30 years  of independence, have repeatedly shown that they will not tolerate anyone who tries to take their country backwards.  \n\nTo all Americans, I will be honest with you, as I’ve always promised. A Russian dictator, invading a foreign country, has costs around the world. \n\nAnd I’m taking robust action to make sure the pain of our sanctions  is targeted at Russia’s economy. And I will use every tool at our disposal to protect American businesses and consumers. \n\nTonight, I can announce that the United States has worked with 30 other countries to release 60 Million barrels of oil from reserves around the world.  \n\nAmerica will lead that effort, releasing 30 Million barrels from our own Strategic Petroleum Reserve. And we stand ready to do more if necessary, unified with our allies.  \n\nThese steps will help blunt gas prices here at home. And I know the news about what’s happening can seem alarming. \n\nBut I want you to know that we are going to be okay.

Content: More support for patients and families. \n\nTo get there, I call on Congress to fund ARPA-H, the Advanced Research Projects Agency for Health. \n\nIt’s based on DARPA—the Defense Department project that led to the Internet, GPS, and so much more.  \n\nARPA-H will have a singular purpose—to drive breakthroughs in cancer, Alzheimer’s, diabetes, and more. \n\nA unity agenda for the nation. \n\nWe can do this. \n\nMy fellow Americans—tonight , we have gathered in a sacred space—the citadel of our democracy. \n\nIn this Capitol, generation after generation, Americans have debated great questions amid great strife, and have done great things. \n\nWe have fought for freedom, expanded liberty, defeated totalitarianism and terror. \n\nAnd built the strongest, freest, and most prosperous nation the world has ever known. \n\nNow is the hour. \n\nOur moment of responsibility. \n\nOur test of resolve and conscience, of history itself. \n\nIt is in this moment that our character is formed. Our purpose is found. Our future is forged. \n\nWell I know this nation.
=========
FINAL ANSWER: The president did not mention Michael Jackson.

QUESTION: {问题}
=========
{总结}
=========
FINAL ANSWER:

-----map_rank ----
使用以下内容来回答最后的问题。如果你不知道答案，就说你不知道，不要试图编造一个答案。
除了给出答案外，还要返回一个分数，说明它对用户的问题的回答有多充分。这应该是以下格式：
问题： [问题在此]。
有帮助的回答： [此处的答案］
分数：[0到100之间的分数］

如何确定分数：
- 更高的是更好的答案
- 较好的回答充分回答了所问的问题，并有足够的细节水平
- 如果你根据上下文不知道答案，那应该是0分。
- 不要过于自信!

例子 #1

语境：
---------
苹果是红色的
---------
问题：苹果是什么颜色的？
有用的答案：红色
分数：100

例子#2

语境：
---------
当时是晚上，证人忘了带眼镜。他不确定那是一辆跑车还是一辆suv。
---------
问题：那辆车是什么类型？
有用的答案：跑车或suv
分数：60

例子#3

语境：
---------
梨子不是红色就是橙色
---------
问题：苹果是什么颜色的？
有用的答案： 该文件没有回答这个问题
分数：0

开始吧!

语境：
---------
[context](语境)
---------
问题： {问题}
有用的答案：

-----map_refine----
"原问题如下： {question}n"
"我们已经提供了一个现有的答案： {现有答案}n"
"我们有机会细化现有的答案"
"（如果需要的话），下面还有一些背景。
"------------\n"
"{context_str}n"
"------------\n"
"考虑到新的背景，完善原来的答案以更好地"
"回答这个问题。"
"如果上下文没有用，就返回原来的答案。"