# KrishiMitra - Technical Specification & Database Design

## 🗄️ Database Schema (For Backend Integration)

### 1. Users Table
```sql
CREATE TABLE users (
    user_id INT PRIMARY KEY AUTO_INCREMENT,
    phone_number VARCHAR(15) UNIQUE NOT NULL,
    name VARCHAR(100),
    language_preference VARCHAR(5) DEFAULT 'en',
    location VARCHAR(200),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    last_login TIMESTAMP,
    is_active BOOLEAN DEFAULT TRUE
);
```

### 2. Farms Table
```sql
CREATE TABLE farms (
    farm_id INT PRIMARY KEY AUTO_INCREMENT,
    user_id INT,
    farm_name VARCHAR(100),
    location VARCHAR(200),
    latitude DECIMAL(10, 8),
    longitude DECIMAL(11, 8),
    total_area DECIMAL(10, 2), -- in hectares
    soil_type ENUM('clay', 'sandy', 'loamy', 'black', 'red', 'alluvial'),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (user_id) REFERENCES users(user_id) ON DELETE CASCADE
);
```

### 3. Crop Recommendations Table
```sql
CREATE TABLE crop_recommendations (
    recommendation_id INT PRIMARY KEY AUTO_INCREMENT,
    user_id INT,
    farm_id INT,
    soil_type VARCHAR(50),
    season VARCHAR(20),
    rainfall DECIMAL(8, 2),
    temperature DECIMAL(5, 2),
    recommended_crops JSON, -- Array of crop objects
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (user_id) REFERENCES users(user_id) ON DELETE CASCADE,
    FOREIGN KEY (farm_id) REFERENCES farms(farm_id) ON DELETE CASCADE
);
```

### 4. Crops Master Table
```sql
CREATE TABLE crops_master (
    crop_id INT PRIMARY KEY AUTO_INCREMENT,
    crop_name VARCHAR(100) NOT NULL,
    crop_name_hi VARCHAR(100), -- Hindi name
    crop_name_mr VARCHAR(100), -- Marathi name
    crop_name_ta VARCHAR(100), -- Tamil name
    scientific_name VARCHAR(150),
    category ENUM('cereal', 'pulse', 'oilseed', 'vegetable', 'fruit', 'cash_crop'),
    suitable_seasons SET('kharif', 'rabi', 'zaid'),
    suitable_soil_types SET('clay', 'sandy', 'loamy', 'black', 'red', 'alluvial'),
    min_temperature DECIMAL(5, 2),
    max_temperature DECIMAL(5, 2),
    min_rainfall DECIMAL(8, 2),
    max_rainfall DECIMAL(8, 2),
    growing_period_days INT,
    water_requirement ENUM('low', 'medium', 'high'),
    description TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

### 5. Weather Data Table
```sql
CREATE TABLE weather_data (
    weather_id INT PRIMARY KEY AUTO_INCREMENT,
    location VARCHAR(200),
    latitude DECIMAL(10, 8),
    longitude DECIMAL(11, 8),
    forecast_date DATE,
    temperature_min DECIMAL(5, 2),
    temperature_max DECIMAL(5, 2),
    humidity DECIMAL(5, 2),
    rainfall DECIMAL(8, 2),
    wind_speed DECIMAL(5, 2),
    weather_condition VARCHAR(50),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    INDEX idx_location_date (location, forecast_date)
);
```

### 6. Fertilizer Recommendations Table
```sql
CREATE TABLE fertilizer_recommendations (
    fertilizer_id INT PRIMARY KEY AUTO_INCREMENT,
    user_id INT,
    farm_id INT,
    crop_type VARCHAR(100),
    growth_stage VARCHAR(50),
    nitrogen_kg DECIMAL(8, 2),
    phosphorus_kg DECIMAL(8, 2),
    potassium_kg DECIMAL(8, 2),
    organic_recommendation TEXT,
    application_date DATE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (user_id) REFERENCES users(user_id) ON DELETE CASCADE,
    FOREIGN KEY (farm_id) REFERENCES farms(farm_id) ON DELETE CASCADE
);
```

### 7. Fertilizer Master Table
```sql
CREATE TABLE fertilizer_master (
    fertilizer_id INT PRIMARY KEY AUTO_INCREMENT,
    fertilizer_name VARCHAR(100),
    fertilizer_name_hi VARCHAR(100),
    fertilizer_name_mr VARCHAR(100),
    fertilizer_name_ta VARCHAR(100),
    type ENUM('organic', 'chemical', 'bio'),
    nitrogen_percentage DECIMAL(5, 2),
    phosphorus_percentage DECIMAL(5, 2),
    potassium_percentage DECIMAL(5, 2),
    application_method TEXT,
    price_per_kg DECIMAL(8, 2),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

### 8. Disease Detection Records
```sql
CREATE TABLE disease_detections (
    detection_id INT PRIMARY KEY AUTO_INCREMENT,
    user_id INT,
    farm_id INT,
    crop_name VARCHAR(100),
    image_path VARCHAR(500),
    detected_diseases JSON, -- Array of disease objects with confidence
    detection_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    status ENUM('detected', 'treated', 'resolved') DEFAULT 'detected',
    notes TEXT,
    FOREIGN KEY (user_id) REFERENCES users(user_id) ON DELETE CASCADE,
    FOREIGN KEY (farm_id) REFERENCES farms(farm_id) ON DELETE CASCADE
);
```

### 9. Disease Master Table
```sql
CREATE TABLE diseases_master (
    disease_id INT PRIMARY KEY AUTO_INCREMENT,
    disease_name VARCHAR(200),
    disease_name_hi VARCHAR(200),
    disease_name_mr VARCHAR(200),
    disease_name_ta VARCHAR(200),
    scientific_name VARCHAR(200),
    affected_crops JSON, -- Array of crop names
    symptoms TEXT,
    causes TEXT,
    chemical_treatment TEXT,
    organic_treatment TEXT,
    prevention_measures TEXT,
    severity ENUM('low', 'medium', 'high', 'critical'),
    image_references JSON, -- Array of reference image URLs
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

### 10. Market Prices Table
```sql
CREATE TABLE market_prices (
    price_id INT PRIMARY KEY AUTO_INCREMENT,
    crop_name VARCHAR(100),
    market_name VARCHAR(200),
    location VARCHAR(200),
    price_per_quintal DECIMAL(10, 2),
    price_date DATE,
    min_price DECIMAL(10, 2),
    max_price DECIMAL(10, 2),
    modal_price DECIMAL(10, 2),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    INDEX idx_crop_date (crop_name, price_date)
);
```

### 11. User Activity Log
```sql
CREATE TABLE activity_log (
    log_id INT PRIMARY KEY AUTO_INCREMENT,
    user_id INT,
    activity_type ENUM('crop_recommendation', 'weather_check', 'fertilizer_advice', 'disease_detection', 'login', 'logout'),
    details JSON,
    ip_address VARCHAR(45),
    user_agent VARCHAR(500),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (user_id) REFERENCES users(user_id) ON DELETE CASCADE
);
```

### 12. Government Schemes Table
```sql
CREATE TABLE government_schemes (
    scheme_id INT PRIMARY KEY AUTO_INCREMENT,
    scheme_name VARCHAR(200),
    scheme_name_hi VARCHAR(200),
    scheme_name_mr VARCHAR(200),
    scheme_name_ta VARCHAR(200),
    description TEXT,
    eligibility_criteria TEXT,
    benefits TEXT,
    application_process TEXT,
    official_website VARCHAR(500),
    contact_info TEXT,
    start_date DATE,
    end_date DATE,
    is_active BOOLEAN DEFAULT TRUE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

## 🔌 API Endpoints Specification

### Authentication APIs

#### 1. User Registration
```http
POST /api/v1/auth/register
Content-Type: application/json

{
  "phone_number": "+919876543210",
  "name": "Rajesh Kumar",
  "language_preference": "hi"
}

Response: 200 OK
{
  "success": true,
  "user_id": 12345,
  "message": "User registered successfully",
  "otp_sent": true
}
```

#### 2. User Login (OTP-based)
```http
POST /api/v1/auth/login
Content-Type: application/json

{
  "phone_number": "+919876543210",
  "otp": "123456"
}

Response: 200 OK
{
  "success": true,
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "user": {
    "user_id": 12345,
    "name": "Rajesh Kumar",
    "language_preference": "hi"
  }
}
```

### Crop Recommendation APIs

#### 3. Get Crop Recommendations
```http
POST /api/v1/crops/recommend
Authorization: Bearer {token}
Content-Type: application/json

{
  "soil_type": "loamy",
  "season": "kharif",
  "rainfall": 800,
  "temperature": 28,
  "farm_id": 456
}

Response: 200 OK
{
  "success": true,
  "recommendations": [
    {
      "crop_id": 1,
      "crop_name": "Rice",
      "crop_name_local": "धान",
      "suitability_score": 95,
      "reason": "Ideal for loamy soil with high rainfall",
      "expected_yield": "40-45 quintals/hectare",
      "market_price": "1850 per quintal",
      "profitability": "high"
    },
    {
      "crop_id": 2,
      "crop_name": "Maize",
      "crop_name_local": "मक्का",
      "suitability_score": 88,
      "reason": "Good for kharif season with moderate water",
      "expected_yield": "35-40 quintals/hectare",
      "market_price": "1650 per quintal",
      "profitability": "medium"
    }
  ]
}
```

#### 4. Get Crop Details
```http
GET /api/v1/crops/{crop_id}?lang=hi
Authorization: Bearer {token}

Response: 200 OK
{
  "success": true,
  "crop": {
    "crop_id": 1,
    "crop_name": "Rice",
    "crop_name_local": "धान",
    "scientific_name": "Oryza sativa",
    "suitable_seasons": ["kharif"],
    "suitable_soil_types": ["clay", "loamy", "alluvial"],
    "temperature_range": "20-35°C",
    "rainfall_requirement": "800-1200mm",
    "growing_period": "120-150 days",
    "fertilizer_requirements": {
      "N": "80-120 kg/ha",
      "P": "40-60 kg/ha",
      "K": "40-60 kg/ha"
    },
    "common_diseases": [
      "Bacterial Leaf Blight",
      "Brown Spot",
      "Blast"
    ]
  }
}
```

### Weather APIs

#### 5. Get Weather Forecast
```http
GET /api/v1/weather/forecast?location=Mumbai&days=7
Authorization: Bearer {token}

Response: 200 OK
{
  "success": true,
  "location": "Mumbai, Maharashtra",
  "coordinates": {
    "latitude": 19.0760,
    "longitude": 72.8777
  },
  "forecast": [
    {
      "date": "2026-02-03",
      "day": "Tuesday",
      "temperature_min": 22,
      "temperature_max": 32,
      "humidity": 65,
      "rainfall": 0,
      "wind_speed": 15,
      "condition": "Partly Cloudy",
      "farming_advice": "Good day for pesticide application"
    },
    // ... 6 more days
  ]
}
```

#### 6. Get Historical Weather Data
```http
GET /api/v1/weather/historical?location=Mumbai&start_date=2026-01-01&end_date=2026-01-31
Authorization: Bearer {token}

Response: 200 OK
{
  "success": true,
  "location": "Mumbai",
  "period": {
    "start": "2026-01-01",
    "end": "2026-01-31"
  },
  "summary": {
    "avg_temperature": 26.5,
    "total_rainfall": 15.2,
    "rainy_days": 3,
    "avg_humidity": 68
  },
  "daily_data": [...]
}
```

### Fertilizer APIs

#### 7. Get Fertilizer Recommendations
```http
POST /api/v1/fertilizer/recommend
Authorization: Bearer {token}
Content-Type: application/json

{
  "crop_type": "rice",
  "growth_stage": "vegetative",
  "soil_type": "loamy",
  "farm_area": 2.5,
  "farm_id": 456
}

Response: 200 OK
{
  "success": true,
  "recommendations": {
    "chemical_fertilizers": [
      {
        "fertilizer_name": "Urea",
        "fertilizer_name_local": "यूरिया",
        "quantity_kg": 100,
        "nitrogen_content": 46,
        "application_method": "Top dressing",
        "timing": "30 days after planting",
        "estimated_cost": 1500
      }
    ],
    "organic_alternatives": [
      {
        "name": "Vermicompost",
        "name_local": "केंचुआ खाद",
        "quantity": "2.5 tons",
        "application_method": "Mix with soil before planting",
        "benefits": "Improves soil structure and microbial activity"
      }
    ],
    "total_cost_estimate": 3750,
    "application_schedule": [...]
  }
}
```

### Disease Detection APIs

#### 8. Detect Disease from Image
```http
POST /api/v1/disease/detect
Authorization: Bearer {token}
Content-Type: multipart/form-data

{
  "image": [binary file],
  "crop_name": "tomato",
  "farm_id": 456
}

Response: 200 OK
{
  "success": true,
  "detection_id": 789,
  "detections": [
    {
      "disease_id": 12,
      "disease_name": "Late Blight",
      "disease_name_local": "झुलसा रोग",
      "confidence": 87.5,
      "severity": "high",
      "symptoms": [
        "Water-soaked lesions on leaves",
        "White fungal growth on undersides",
        "Dark brown spots on stems"
      ],
      "treatment": {
        "chemical": "Apply Mancozeb 75% WP @ 2.5g/liter water",
        "organic": "Spray Bordeaux mixture (1% solution)",
        "preventive": "Remove infected plants, improve drainage"
      },
      "treatment_cost_estimate": 500,
      "success_rate": "85%"
    },
    {
      "disease_id": 15,
      "disease_name": "Powdery Mildew",
      "disease_name_local": "पाउडरी फफूंदी",
      "confidence": 65.2,
      "severity": "medium",
      // ... similar structure
    }
  ],
  "image_url": "https://storage.example.com/detections/789.jpg"
}
```

#### 9. Get Disease History
```http
GET /api/v1/disease/history?farm_id=456&limit=10
Authorization: Bearer {token}

Response: 200 OK
{
  "success": true,
  "total_detections": 25,
  "history": [
    {
      "detection_id": 789,
      "detection_date": "2026-02-01T10:30:00Z",
      "crop_name": "tomato",
      "detected_diseases": ["Late Blight"],
      "status": "treated",
      "image_url": "...",
      "notes": "Treatment applied on 2026-02-02"
    },
    // ... more records
  ]
}
```

### Market Price APIs

#### 10. Get Current Market Prices
```http
GET /api/v1/market/prices?crop=rice&location=Mumbai
Authorization: Bearer {token}

Response: 200 OK
{
  "success": true,
  "crop": "Rice",
  "location": "Mumbai",
  "prices": [
    {
      "market_name": "Vashi APMC",
      "date": "2026-02-02",
      "price_per_quintal": 1850,
      "min_price": 1800,
      "max_price": 1900,
      "trend": "stable",
      "volume_traded": "5000 quintals"
    },
    // ... more markets
  ],
  "price_trend_7days": [1820, 1830, 1845, 1850, 1850, 1860, 1850],
  "prediction_next_week": "Prices expected to remain stable"
}
```

### Government Schemes APIs

#### 11. Get Applicable Schemes
```http
GET /api/v1/schemes/applicable?user_id=12345&crop_type=rice
Authorization: Bearer {token}

Response: 200 OK
{
  "success": true,
  "schemes": [
    {
      "scheme_id": 1,
      "scheme_name": "PM-KISAN",
      "scheme_name_local": "प्रधानमंत्री किसान सम्मान निधि",
      "description": "Direct income support to farmers",
      "benefits": "₹6000 per year in three installments",
      "eligibility": "All land-holding farmer families",
      "how_to_apply": "Visit nearest CSC or apply online",
      "official_website": "https://pmkisan.gov.in",
      "contact": "1800-180-1551"
    },
    // ... more schemes
  ]
}
```

## 🔐 Authentication & Security

### JWT Token Structure
```json
{
  "user_id": 12345,
  "phone_number": "+919876543210",
  "role": "farmer",
  "iat": 1675328400,
  "exp": 1675414800
}
```

### Security Measures
1. **Password Hashing**: bcrypt with salt rounds = 10
2. **OTP**: 6-digit, expires in 10 minutes
3. **Rate Limiting**: 
   - Login attempts: 5 per 15 minutes
   - API calls: 100 per hour per user
4. **CORS**: Configured for specific domains
5. **Input Validation**: All inputs sanitized
6. **SQL Injection Protection**: Prepared statements
7. **XSS Protection**: Content Security Policy headers

## 📊 ML Model Integration

### Disease Detection Model
```python
# Model Architecture
- Input: Image (224x224x3)
- Base Model: ResNet50 / MobileNetV2
- Custom Layers: Dense(512) → Dropout(0.5) → Dense(num_classes)
- Output: Disease probabilities

# Training Data
- 50,000+ labeled crop disease images
- 20 common diseases across 10 crops
- Augmentation: rotation, flip, brightness, contrast

# Deployment
- Format: TensorFlow Lite / ONNX
- Inference: <2 seconds per image
- Accuracy: 87% on test set
```

### Crop Recommendation Model
```python
# Features
- Soil parameters (NPK, pH, organic carbon)
- Climate data (temperature, rainfall, humidity)
- Historical yield data
- Market prices

# Algorithm
- Random Forest Classifier
- Feature importance ranking
- Ensemble of multiple models

# Output
- Top 5 crop recommendations
- Confidence scores
- Expected yield ranges
```

## 🌐 External API Integrations

### 1. Weather API
- **Provider**: OpenWeatherMap / WeatherAPI
- **Endpoints**: Current weather, 7-day forecast
- **Update Frequency**: Every 3 hours
- **Caching**: Redis, 1-hour TTL

### 2. Market Price API
- **Provider**: Government APMC portals
- **Data**: Daily market prices
- **Update Frequency**: Daily at 6 PM
- **Storage**: Historical data for trend analysis

### 3. SMS Gateway
- **Provider**: Twilio / MSG91
- **Usage**: OTP, alerts, notifications
- **Rate**: Transactional SMS only

### 4. Cloud Storage
- **Provider**: AWS S3 / Google Cloud Storage
- **Usage**: Disease detection images, user uploads
- **Configuration**: Private buckets, signed URLs

## 📱 Mobile App API Considerations

### Push Notifications
```json
{
  "type": "weather_alert",
  "title": "Heavy Rain Alert",
  "body": "Heavy rainfall expected in next 24 hours. Plan accordingly.",
  "data": {
    "location": "Mumbai",
    "severity": "high",
    "action_url": "/weather/forecast"
  }
}
```

### Offline Sync
- Store last 7 days of data locally
- Sync on network availability
- Queue API calls for later execution
- Delta sync for efficiency

## 🔧 Deployment Configuration

### Production Environment Variables
```env
# Database
DB_HOST=localhost
DB_PORT=3306
DB_NAME=krishimitra_prod
DB_USER=krishimitra_user
DB_PASSWORD=secure_password

# JWT
JWT_SECRET=your_jwt_secret_key
JWT_EXPIRY=24h

# External APIs
WEATHER_API_KEY=your_weather_api_key
SMS_API_KEY=your_sms_api_key

# Storage
AWS_ACCESS_KEY=your_aws_key
AWS_SECRET_KEY=your_aws_secret
S3_BUCKET_NAME=krishimitra-uploads

# App Config
NODE_ENV=production
PORT=3000
RATE_LIMIT_WINDOW=15
RATE_LIMIT_MAX=100
```

### Nginx Configuration
```nginx
server {
    listen 80;
    server_name api.krishimitra.com;
    
    location / {
        proxy_pass http://localhost:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
    
    # Rate limiting
    limit_req_zone $binary_remote_addr zone=api:10m rate=100r/m;
    limit_req zone=api burst=20;
}
```

## 📈 Monitoring & Analytics

### Key Metrics to Track
1. **User Metrics**:
   - Daily Active Users (DAU)
   - Monthly Active Users (MAU)
   - User retention rate
   - Feature usage statistics

2. **Performance Metrics**:
   - API response times
   - Database query performance
   - ML model inference time
   - Error rates

3. **Business Metrics**:
   - Crop recommendations accuracy
   - Disease detection success rate
   - User satisfaction scores
   - Feature adoption rates

### Logging Structure
```json
{
  "timestamp": "2026-02-02T10:30:45Z",
  "level": "info",
  "service": "crop-recommendation",
  "user_id": 12345,
  "action": "get_recommendations",
  "request_id": "abc-123-def",
  "duration_ms": 245,
  "status": "success",
  "metadata": {
    "soil_type": "loamy",
    "season": "kharif"
  }
}
```

## 🧪 Testing Strategy

### Unit Tests
- Database models
- Business logic functions
- Utility functions
- API request/response formatting

### Integration Tests
- API endpoint testing
- Database operations
- External API mocking
- Authentication flows

### End-to-End Tests
- User registration to crop recommendation
- Disease detection flow
- Weather forecast retrieval
- Multi-language support

### Load Testing
- Concurrent users: 10,000+
- API throughput: 1000 requests/second
- Database connections: 100 pool size
- Response time: <500ms for 95th percentile

---

This technical specification provides a comprehensive foundation for building a production-ready backend system for the KrishiMitra Farmer Assistance System.
