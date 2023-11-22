چه زمانی باید از دستور 'clean DAM_data' برای پاکسازی Guardium استفاده کنم؟
عیب یابی


پایگاه داده در دستگاه IBM Security Guardium بیش از 90٪ پر است. روش‌های عادی پاکسازی داده‌های کافی را حذف نکردند.

علت
فرآیند پاکسازی برنامه ریزی نشده بود یا ناموفق بود
پاکسازی در حال اجرا است اما ساعت ها طول می کشد
Purge به درستی کار می کند، اما حجم زیادی از داده ها در چند روز گذشته ثبت شده است
یک جدول واحد تقریباً تمام فضای پایگاه داده را اشغال می کند

تشخیص مشکل
اگر دستگاه در حالت بازیابی نیست و پورتال رابط کاربری گرافیکی هنوز در دسترس است، برای جزئیات بیشتر در مورد آخرین پاکسازی، گزارش تجمع/بایگانی را بررسی کنید. همچنین، تنظیمات پاکسازی و زمان‌بندی را در قسمت Data Archive بررسی کنید.

از CLI، این دستورات را اجرا کنید و خروجی را بررسی کنید.
support show db-top-tables all

support show large_files 500 0
فایل های بزرگ شامل پارتیشن های داده از جداول بزرگ است. نام فایل ها شامل تاریخ ثبت اطلاعات است.

حل مشکل
هشدار - فقط زمانی از دستور "clean DAM_data" استفاده کنید که همه گزینه‌های دیگر تمام شده باشند. برخلاف فرآیند پاکسازی عادی، این دستور تضمین نمی کند که داده ها به درستی بایگانی شده اند، بنابراین می تواند منجر به از دست رفتن دائمی داده های حسابرسی شود. قبل از اجرای این دستور، با پشتیبانی IBM تماس بگیرید. اگر کیس را باز کردید، خروجی این دستورات را نیز وارد کنید.
 
support must_gather system-db-info

support must_gather sniffer_issues

 
یادگیری توصیه شده

اگر دیدم دستگاه Guardium من پر شده است، چه کاری می توانم انجام دهم؟
استفاده از DAM_data تمیز برای پاکسازی داده ها در یک دستگاه کامل Guardium
پیکربندی و کنترل دستورات CLI
IBM MustGather: جمع آوری داده ها برای Guardium Appliance


اجرای فرمان از CLI
از "Clean DAM_data" برای حذف سخت روزهای خاصی از داده ها از جداول خاص استفاده کنید. از CLI، این دستور را با گزینه purge_type که پشتیبانی IBM توصیه می‌شود، صادر کنید. تمام رکوردهای بین تاریخ شروع و پایان حذف می شوند. پارامتر purge_type کنترل می کند که کدام جداول پاک شوند.

support clean DAM_data full_details 2022-01-19 2022-01-20

You are about to delete audit records outside of the standard purging policy.
It is highly recommended to consult with Guardium Services before running this command.
Please type "confirm delete" to approve the cleanup action. 

confirm delete

This may take a while to complete. Please check the log /var/log/guard/agg_purge_data.log
ok

از فایل سرور برای بررسی agg_purge_data.log برای پیشرفت استفاده کنید.

اگر فضای دیسک کافی برای پاک کردن یک روز از یک جدول وجود ندارد، از این grdapi برای پاک کردن داده ها در قطعات کوچکتر استفاده کنید.

guard.com> grdapi get_purge_batch_size
ID=0
Purge Batch Size = 200000
ok
guard.com> grdapi set_purge_batch_size batchSize=100
ID=0
ok
guard.com> grdapi get_purge_batch_size
ID=0
Purge Batch Size = 100
ok
guard.com> 


سپس، دوباره "پاک کردن DAM_data" را امتحان کنید. هنگامی که جدول کوچکتر شد و فضای دیسک آزاد بیشتری وجود داشت، از یک اندازه بزرگتر برای تسریع پاکسازی استفاده کنید. هنگامی که فضای دیسک کافی برای عملکرد بهینه پاکسازی وجود داشت، اندازه دسته را به 200000 بازنشانی کنید.
