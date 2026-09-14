[9/14/2026 7:35 PM] Talal: س1: الـEntities الرئيسية؟
الكيان المركزي هو التاجر (merchantId موجود كـ foreign key ضمني في كل جدول تقريباً، لكن لا يوجد جدول merchant منفصل حالياً — فقط brand_profile). حول التاجر تدور 4 مجموعات:
المحتوى: content_event، video_asset، content_chunk، content_dna، content_plan، content_blueprint
المال: revenue_event، ad_spend_event، csv_upload
التحليل: attribution، comment_analysis، winning_pattern، event_context
التغذية الراجعة: campaign_recommendation، campaign_outcome_feedback، user_feedback
س2: بيانات كل User (تاجر)؟
موجودة في brand_profile: اسم البراند، حسابات السوشال (تيك توك/انستقرام/X)، مرحلة العمل، وصف المنتج، منصات البيع، التحدي الرئيسي، الميزانية الإعلانية الشهرية، صوت البراند، الجمهور المستهدف، القيم، أهداف الحملات.
س3: بيانات كل Post/Content؟
content_event: المنصة المصدر، رابط المنشور، الكابشن، الهاشتاقات، أكواد الخصم المكتشفة، التفاعل (لايكات/تعليقات/مشاركات)، تاريخ النشر، حالة المعالجة، رابط الميديا/الفيديو، المدة، الترانسكربت.
س4: بيانات ناتجة عن التحليل يجب حفظها؟
content_dna: 16 بُعد تصنيفي (hook_type، content_type، pain_point، emotion_type... إلخ) + ملخص نصي + embedding للتشابه
attribution: نسبة الإيراد للمحتوى مع درجة ثقة
comment_analysis: مشاعر التعليقات، ثيمات الصوت، فجوات المنتج
winning_pattern: الأبعاد التي ترتبط فعلياً بالمبيعات (winner مقابل views_only)
س5: المعرّف الفريد لكل Entity؟
كل الجداول تستخدم uuid كـ primary key (defaultRandom()). التاجر نفسه معرّف بـ merchantId (uuid) لكنه غير مربوط حالياً بجدول merchant أو نظام مصادقة موحّد — هذه فجوة تصميمية ملحوظة.
1. العلاقات والـHistory
س6: كيف ترتبط الـEntities؟
علاقات foreign-key ضمنية (uuid حقول، بدون قيود FK فعلية معرّفة في الـ schema):
merchant → * (one-to-many على كل الجداول عبر merchantId)
content_event → content_dna / video_asset / attribution / comment_analysis (one-to-one غالباً)
content_event → content_chunk (one-to-many، كل مقطع من الفيديو)
csv_upload → revenue_event / ad_spend_event (one-to-many)
content_blueprint → campaign_outcome_feedback (one-to-many)
س7: هل User يملك أكثر من Post؟
نعم، علاقة one-to-many عبر merchantId في content_event.
س8: هل Post له أكثر من تحليل؟
حالياً لا — content_dna وattribution وcomment_analysis كل واحد صف واحد لكل content_event_id (لا يوجد تاريخ نسخ تحليل). هذه بالضبط الفجوة التي تعالجها وثيقة إعادة التصميم.
س9: نحتاج الاحتفاظ بتاريخ الخطط والتحليلات السابقة؟
حالياً لا يوجد — كل تحليل يستبدل (أو يُفترض يُستبدل) السابق. وثيقة DATABASE_REDESIGN.md تقترح content_performance_summary وrecommendation_impact بالضبط لهذا السبب: بدون تاريخ، ما نقدر نقيس هل التوصية أثّرت فعلاً، ولا نكتشف اتجاهات الجمهور بمرور الوقت. هذا تغيير مو مطبّق بعد.
1. العمليات والتحديث
س10: العمليات التي يحتاجها النظام؟
Ingest: رفع CSV → revenue_event/ad_spend_event؛ سحب محتوى → content_event
Process: تحليل فيديو (Gemini) → content_dna؛ حساب attribution؛ تحليل تعليقات
Discover: اكتشاف الأنماط الرابحة → winning_pattern
Recommend: توليد campaign_recommendation / content_blueprint
Feedback loop: campaign_outcome_feedback، user_feedback
س11: البيانات التي تتحدث باستمرار؟
processing_status وstatus في content_event/video_asset (queued→...→done)، merchant_acknowledged في campaign_recommendation، والمقاييس المتراكمة (تفاعل، إيراد) مع كل رفع CSV جديد.
س12: البيانات التي تُسجّل مرة واحدة؟
brand_profile (يتحدّث نادراً)، content_dna بعد التحليل (immutable snapshot)، revenue_event/ad_spend_event (سجلات تاريخية ثابتة بمجرد الاستيراد).
س13: إلزامية مقابل اختيارية؟
إلزامي دائماً: merchantId، معرّفات الوقت (createdAt)، القيم المالية (revenueAmountSar, amountSpentSar — NOT NULL). اختياري بكثرة: كل حقول DNA (hookType, painPoint...) لأنها تُملأ تدريجياً حسب توفر التحليل، وpostUrl/caption لأن بعض المحتوى قد يكون "generated" لا مصدر خارجي له.
1. الـAI والتحليل
س14: الـMetrics التي يعتمد عليها الـAI؟
من winning_pattern: avgRevenue، revenueLift، engagementLift، sampleSize — مصمّمة خصيصاً لفصل "يبيع فعلاً" عن "يجيب مشاهدات بس" (classification: winner/views_only/neutral). التصنيف ما يعتمد على "إيراد > 0" بل على طبقات أداء (Top/Average/Under).
س15: مخرجات الـAI التي نحتاج حفظها؟
[9/14/2026 7:35 PM] Talal: موجود حالياً: content_dna (16 بُعد + embedding)، winning_pattern، content_blueprint (سكربتات مولّدة + درجة محاكاة الجمهور)، comment_analysis. غير موجود بعد وموثّق في وثيقة إعادة التصميم: جدول ai_insight عام يسجّل كل insight ولّده الذكاء ويتتبع dismissed/saved/acted_upon/feedback_rating — هذا مفقود حالياً، كل نوع insight له جدوله الخاص بدل سجل موحّد.
س16: نحتاج تاريخ نتائج وتحليلات الـAI؟
نعم، وهذه أكبر فجوة حالية. لا يوجد أي جدول يحفظ نسخ متعددة من نفس التحليل بمرور الوقت — لا لـ DNA، ولا للتوصيات، ولا لقياس الأثر. recommendation_impact (مقترح، غير مطبّق) هو بالضبط حلقة التعلّم المفقودة: يقيس ما حصل فعلياً بعد تطبيق توصية، بدونها الـAI لا يتحسّن بمرور الوقت — فقط يولّد ويُنسى.
ملخص الفجوة الأساسية: الـschema الحالي مصمم لـ"جمع وتحليل لحظي" — كل تحليل يمثّل حالة واحدة بلا تاريخ. الفجوات الموثّقة في DATABASE_REDESIGN.md (8 تغييرات، لم تُطبّق) تحديداً تعالج أسئلة 8، 9، 15، 16 عبر إضافة ai_insight، recommendation_impact، content_performance_summary، وmerchant_goal
من أين تأتي البيانات؟
الاتجاه المستهدف: الاعتماد على TikTok API الرسمي لبيانات المحتوى، وAPI من زد وسلة لبيانات المبيعات، بدل الاعتماد الحالي على scraping وملفات CSV يدوية.
الوضع الحالي بالكود (3 مصادر):
1. API خارجي (Scraping): Apify لسحب تعليقات تيك توك/انستقرام، وGemini API لتحليل الفيديو واستخراج DNA.
2. ملفات CSV: رفع يدوي من التاجر لبيانات الإيراد والإنفاق الإعلاني → csv_upload → revenue_event/ad_spend_event.
هل توجد أرقام جوالات أو هويات أو عناوين؟
لا — ضمن جداول التاجر والمحتوى، ما فيه حقول أرقام جوال أو هوية أو عنوان. الحقل الوحيد القريب من بيانات شخصية هو revenueEvent.customerRef، وهو هاش (hash) — دالة تحويل أحادية الاتجاه تحوّل معرّف العميل الأصلي إلى قيمة غير قابلة للعكس، ما تقدر ترجعها لرقم/بريد العميل الحقيقي. غير قابل للربط عبر الرفعات المختلفة (نفس العميل بالرفعة الثانية ياخذ قيمة مختلفة).
هل يجب تسجيل عمليات الوصول للبيانات؟
نعم، يجب — أمنياً هذا ضروري خصوصاً إن البيانات تشمل مبيعات وأداء تجار حساس. حالياً لا يوجد أي سجل وصول لبيانات التاجر (من قرأ بيانات أي تاجر ومتى) — الموجود فقط founder_audit_log وهو يسجّل تعديلات لوحة الفاؤندر، مو الوصول للقراءة على بيانات التجار. هذه فجوة يفترض تُسد: تسجيل كل عملية وصول (قراءة) لبيانات تاجر — مين وصل، أي بيانات، ومتى.
