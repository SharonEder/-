
אפליקציה לתאום תאריכים
import React, { useState, useEffect } from 'react';
import { Calendar, Users, Check, X, Star } from 'lucide-react';

const CelebrationScheduler = () => {
  const [friends] = useState([
    'ליה (החוגגת)', 'שרון', 'אלה', 'יערה', 'טלי', 'שירי', 'הדס', 'אלינור'
  ]);
  
  const [currentFriend, setCurrentFriend] = useState(0);
  const [availability, setAvailability] = useState({});
  const [dates, setDates] = useState([]);

  // יצירת תאריכים ביוני ויולי
  useEffect(() => {
    const dateList = [];
    const currentYear = 2025;
    
    // יוני 2025
    for (let day = 1; day <= 30; day++) {
      const date = new Date(currentYear, 5, day); // חודש 5 = יוני
      dateList.push(date);
    }
    
    // יולי 2025
    for (let day = 1; day <= 31; day++) {
      const date = new Date(currentYear, 6, day); // חודש 6 = יולי
      dateList.push(date);
    }
    
    setDates(dateList);
  }, []);

  const formatDate = (date) => {
    const days = ['ראשון', 'שני', 'שלישי', 'רביעי', 'חמישי', 'שישי', 'שבת'];
    const months = ['ינואר', 'פברואר', 'מרץ', 'אפריל', 'מאי', 'יוני', 
                   'יולי', 'אוגוסט', 'ספטמבר', 'אוקטובר', 'נובמבר', 'דצמבר'];
    
    return `${days[date.getDay()]}, ${date.getDate()} ${months[date.getMonth()]}`;
  };

  const getDateKey = (date) => {
    return date.toISOString().split('T')[0];
  };

  const toggleAvailability = (dateKey) => {
    const friendName = friends[currentFriend];
    setAvailability(prev => ({
      ...prev,
      [friendName]: {
        ...prev[friendName],
        [dateKey]: !prev[friendName]?.[dateKey]
      }
    }));
  };

  const getAvailabilityCount = (dateKey) => {
    return friends.reduce((count, friend) => {
      return count + (availability[friend]?.[dateKey] ? 1 : 0);
    }, 0);
  };

  const getBestDates = () => {
    return dates
      .map(date => ({
        date,
        count: getAvailabilityCount(getDateKey(date))
      }))
      .filter(item => item.count > 0)
      .sort((a, b) => b.count - a.count);
  };

  const nextFriend = () => {
    if (currentFriend < friends.length - 1) {
      setCurrentFriend(currentFriend + 1);
    }
  };

  const prevFriend = () => {
    if (currentFriend > 0) {
      setCurrentFriend(currentFriend - 1);
    }
  };

  const resetAll = () => {
    setAvailability({});
    setCurrentFriend(0);
  };

  const bestDates = getBestDates();
  const perfectDates = bestDates.filter(item => item.count === 8);

  return (
    <div className="min-h-screen bg-gradient-to-br from-pink-50 to-purple-100 p-4" dir="rtl">
      <div className="max-w-4xl mx-auto">
        <div className="bg-white rounded-xl shadow-lg p-6 mb-6">
          <div className="flex items-center justify-center mb-6">
            <Calendar className="text-purple-600 ml-2" size={32} />
            <h1 className="text-3xl font-bold text-purple-800">מתאם תאריכים לחגיגה</h1>
          </div>
          
          {/* בחירת חברה נוכחית */}
          <div className="bg-purple-50 rounded-lg p-4 mb-6">
            <div className="flex items-center justify-between mb-4">
              <h2 className="text-xl font-semibold text-purple-800 flex items-center">
                <Users className="ml-2" size={20} />
                {friends[currentFriend]} - סמני את התאריכים שמתאימים לך
              </h2>
              <div className="text-sm text-purple-600">
                {currentFriend + 1} מתוך {friends.length}
              </div>
            </div>
            
            <div className="flex justify-between">
              <button
                onClick={prevFriend}
                disabled={currentFriend === 0}
                className="px-4 py-2 bg-purple-500 text-white rounded-lg disabled:bg-gray-300 disabled:cursor-not-allowed hover:bg-purple-600 transition-colors"
              >
                ← הקודמת
              </button>
              <button
                onClick={nextFriend}
                disabled={currentFriend === friends.length - 1}
                className="px-4 py-2 bg-purple-500 text-white rounded-lg disabled:bg-gray-300 disabled:cursor-not-allowed hover:bg-purple-600 transition-colors"
              >
                הבאה →
              </button>
            </div>
          </div>

          {/* רשימת תאריכים */}
          <div className="grid gap-3 mb-6">
            <h3 className="text-lg font-semibold text-gray-700 mb-2">בחרי תאריכים מתאימים:</h3>
            {dates.map((date) => {
              const dateKey = getDateKey(date);
              const isSelected = availability[friends[currentFriend]]?.[dateKey];
              const availableCount = getAvailabilityCount(dateKey);
              
              return (
                <div
                  key={dateKey}
                  onClick={() => toggleAvailability(dateKey)}
                  className={`p-4 rounded-lg border-2 cursor-pointer transition-all hover:shadow-md ${
                    isSelected 
                      ? 'border-green-500 bg-green-50' 
                      : 'border-gray-200 bg-white hover:border-purple-300'
                  }`}
                >
                  <div className="flex justify-between items-center">
                    <div className="flex items-center">
                      {isSelected ? (
                        <Check className="text-green-600 ml-2" size={20} />
                      ) : (
                        <div className="w-5 h-5 border-2 border-gray-300 rounded ml-2"></div>
                      )}
                      <span className="text-lg font-medium">{formatDate(date)}</span>
                    </div>
                    <div className="flex items-center">
                      <span className="text-sm text-gray-600 ml-2">{availableCount}/8</span>
                      <div className="w-4 h-4 bg-purple-200 rounded-full flex items-center justify-center">
                        <div 
                          className="bg-purple-500 rounded-full transition-all"
                          style={{
                            width: `${(availableCount / 8) * 100}%`,
                            height: `${(availableCount / 8) * 100}%`
                          }}
                        ></div>
                      </div>
                    </div>
                  </div>
                </div>
              );
            })}
          </div>

          {/* כפתור מציאת תאריכים משותפים */}
          <div className="flex justify-center mb-6">
            <button
              onClick={() => {
                const resultsSection = document.getElementById('results-section');
                if (resultsSection) {
                  resultsSection.scrollIntoView({ behavior: 'smooth' });
                }
              }}
              className="px-8 py-4 bg-gradient-to-r from-purple-500 to-pink-500 text-white rounded-lg font-bold text-lg hover:from-purple-600 hover:to-pink-600 transition-all transform hover:scale-105 shadow-lg flex items-center"
            >
              <Star className="text-yellow-300 ml-2" size={24} />
              🔍 מצאי תאריכים משותפים!
            </button>
          </div>

          {/* תוצאות */}
          {bestDates.length > 0 && (
            <div id="results-section" className="bg-gradient-to-r from-purple-50 to-pink-50 rounded-lg p-6 border-4 border-purple-200 shadow-xl">
              <h3 className="text-2xl font-bold text-purple-800 mb-6 flex items-center justify-center">
                <Star className="text-yellow-500 ml-2" size={32} />
                🎯 התוצאות שלכן!
              </h3>
              
              {perfectDates.length > 0 && (
                <div className="mb-6">
                  <div className="text-center mb-4">
                    <h4 className="text-2xl font-bold text-green-700 mb-2 flex items-center justify-center">
                      🏆 יש לנו תאריכים מושלמים! 🏆
                    </h4>
                    <p className="text-lg text-green-600 font-semibold">כל 8 הבנות פנויות בתאריכים האלה! 🎉</p>
                  </div>
                  <div className="space-y-3">
                    {perfectDates.map(({ date, count }) => (
                      <div key={getDateKey(date)} className="bg-gradient-to-r from-green-100 to-emerald-100 border-4 border-green-400 rounded-xl p-4 shadow-lg transform hover:scale-102 transition-all">
                        <div className="flex justify-between items-center">
                          <span className="font-bold text-green-800 text-xl">{formatDate(date)}</span>
                          <div className="flex items-center">
                            <span className="bg-green-500 text-white px-4 py-2 rounded-full text-lg font-bold mr-2">
                              {count}/8 ✨
                            </span>
                            <span className="text-2xl">🎊</span>
                          </div>
                        </div>
                      </div>
                    ))}
                  </div>
                </div>
              )}
              
              {bestDates.filter(item => item.count < 8).length > 0 && (
                <div>
                  <div className="text-center mb-4">
                    <h4 className="text-xl font-semibold text-purple-700 mb-2">📅 תאריכים אחרים (לא כל הבנות פנויות)</h4>
                    <p className="text-purple-600">אם התאריכים המושלמים לא מתאימים...</p>
                  </div>
                  <div className="space-y-2">
                    {bestDates.filter(item => item.count < 8).slice(0, 5).map(({ date, count }) => (
                      <div key={getDateKey(date)} className="bg-white border-2 border-purple-200 rounded-lg p-3 hover:border-purple-400 transition-all">
                        <div className="flex justify-between items-center">
                          <span className="font-medium text-lg">{formatDate(date)}</span>
                          <span className="bg-purple-500 text-white px-3 py-1 rounded-full text-sm font-semibold">
                            {count}/8 בנות פנויות
                          </span>
                        </div>
                      </div>
                    ))}
                  </div>
                </div>
              )}
            </div>
          )}

          {/* כפתור איפוס */}
          <div className="flex justify-center mt-6">
            <button
              onClick={resetAll}
              className="px-6 py-2 bg-red-500 text-white rounded-lg hover:bg-red-600 transition-colors flex items-center"
            >
              <X className="ml-2" size={16} />
              איפוס הכל
            </button>
          </div>
        </div>
      </div>
    </div>
  );
};

export default CelebrationScheduler;
