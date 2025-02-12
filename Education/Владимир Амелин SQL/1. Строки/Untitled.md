select

actor_id actorIdentity,

first_name as firstName,

last_name "Last Name",

last_update as "Last Update"

from

actor;

  

  

select

amount,

amount + 2,

amount - 3,

amount * 1,5,

amount /33,

amount ^ 2,

mod(amount, 4),

amount % 4,

div(amount,2),

round(amount /2,1),

floor(amount / 2),

ceil(amount / 2)

from

public.payment;

  

  

select

first_name,

last_name,

concat(first_name,' ', last_name),

first_name || ' ' || last_name,

char_length(first_name),

trim(trailing ' ' || first_name || ' '),

substring(first_name, 1, 3) || ' ' || substring(last_name, 1, 3),

upper(first_name),

lower(first_name),

trim('er' from first_name)

from

actor

select

email,

substring(email, 1, strpos(email, '@') - 1),

strpos(email, '@'),

substring(email, strpos(email, '@') + 1)

from

staff