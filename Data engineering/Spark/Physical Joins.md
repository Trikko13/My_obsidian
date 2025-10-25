8. Типы физических джоинов (что и когда)
    

- Broadcast Hash Join (BHJ): маленькая «build» сторона; избегаем shuffle большой; лучший выбор при небольшом размере.
    
- Sort Merge Join (SMJ): для больших сторон на equi-join; требует shuffle+sort; хорош на больших партициях/бакетах.
    
- Shuffle Hash Join (SHJ): обе стороны шaффлятся, затем строится hash по партициям; может быть быстрее SMJ при хорошем fit в память, но чувствителен к skew.
    
- Broadcast Nested Loop Join (BNLJ): для non-equi условий с малой build стороной; риск O(N×M) — применять осмотрительно.
    
- Shuffled Nested Loop / Cartesian Product: крайние случаи без условий; избегать в проде.