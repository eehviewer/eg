 // 加载考试数据
    useEffect(() => {
        const fetchExam = async () => {
            try {
                const [examRes, questionsRes] = await Promise all([
                    axios get(`/api/exams/${examId}`),
                    axios get(`/api/exams/${examId}/questions`)
                ]);
                
                setExam(examRes data);
                setQuestions(questionsRes data);
                
                // 初始化答案对象
                const initialAnswers = {};
                questionsRes data forEach(q => {
                    initialAnswers[q question_id] = 
                        q type === 'single_choice' || q type === 'true_false' ? '' : null;
                });
                setAnswers(initialAnswers);
                
                // 设置考试时长倒计时
                if (examRes data duration) {
                    setti meLeft(examRes data duration * 60);
                }
                
            } catch (err) {
                setError('加载考试数据失败');
                console error(err);
            }
        };
        
        fetchExam();
    }, [examId]);
 
    // 倒计时逻辑
    useEffect(() => {
        let ti mer;
        if (ti meLeft > 0 && !isSubmitted) {
            ti mer = setInterval(() => {
                setti meLeft(prev => {
                    if (prev <= 1) {
                        clearInterval(ti mer);
                        autoSubmit();
                        return 0;
                    }
                    return prev - 1;
                });
            }, 1000);
        }
