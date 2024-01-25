# 

[![](https://mermaid.ink/img/pako:eNpNj8uqwkAQRH-l6ZWCj30WytWIS4Vk54g0mdaomQdjB5Ek_35HI5JeFVWHA91g4TRjgufKPYuSgkCeKgvx_kYKt5sc5renzHzpFY5hOl3AarTfZbGuHOmTYaFxz68-K6ybnKkoOYAL8JBas5Vl1yPrN9J-9xbSw8AkfXvy5Dkch3zWS1rYDPmv-scrixM0HAxddfyneRsUSsmGFSYxagp3hcp2kaNaXPayBSYSap5g7TUJp1e6BDKYnKl6cPcPhEdcYg?type=png)](https://mermaid.live/edit#pako:eNpNj8uqwkAQRH-l6ZWCj30WytWIS4Vk54g0mdaomQdjB5Ek_35HI5JeFVWHA91g4TRjgufKPYuSgkCeKgvx_kYKt5sc5renzHzpFY5hOl3AarTfZbGuHOmTYaFxz68-K6ybnKkoOYAL8JBas5Vl1yPrN9J-9xbSw8AkfXvy5Dkch3zWS1rYDPmv-scrixM0HAxddfyneRsUSsmGFSYxagp3hcp2kaNaXPayBSYSap5g7TUJp1e6BDKYnKl6cPcPhEdcYg)

> In /papers, we got a serial calls of APIs, `/jwt.php`->`/load_meta`->`load_xxx_paper`. This sequence is flawed. 
>
> I don't the know /jwt.php very much, if I can get a token with /jwt.php, that means the server can recognize me, that why didn't it tag me with the token when I go to "https://e.star.hkecity.hk"? Why can't it save the token in my cookie when I first go to it?
> 
> We can't parallelize these calls despite the fact that `load_xxx_paper` has nothing to do with `load_meta`, the server already knows who I am, that means server can just return `Paper[]` from endpoints like `/my_papers`, `student_corner_papers` and so on. The hidden problem is that `TeacherPaper` and `StudentPaper` are different. There are fields that secifically for teacher and student. This make it not type-safe in code.
