# poepart3
namespace RaceDayMVC.Models
{
    public class EnrollmentViewModel
    {
       
            public int EntryId { get; set; }
            public int RaceNumber { get; set; }

            
            public string EventName { get; set; }
            public DateTime EventDate { get; set; }

            
            public string CategoryName { get; set; }

            
            public string UserFullName { get; set; }
            public string UserEmail { get; set; }


namespace RaceDayMVC.Models
{
    public class ErrorViewModel
    {
        public string? RequestId { get; set; }

        public bool ShowRequestId => !string.IsNullOrEmpty(RequestId);
    }
}
    namespace RaceDayMVC.Models
{
    public class EventCategoryViewModel
    {
       
            public int EventCategoryId { get; set; }
            public string CategoryName { get; set; }
            public decimal DistanceKm { get; set; }
            public decimal EntryFee { get; set; }
            public int Capacity { get; set; }
            public int EntriesCount { get; set; }

           
            public bool IsFull => EntriesCount >= Capacity;
        }
    }        
using System.ComponentModel.DataAnnotations;

namespace RaceDayMVC.Models
{
    
        public class EventViewModel
        {
            public int EventId { get; set; }

            [Required]
            [Display(Name = "Event Name")]
            public string EventName { get; set; }

            [Required]
            [Display(Name = "Date")]
            [DataType(DataType.Date)]
            public DateTime EventDate { get; set; }

            [Required]
            public string City { get; set; }

            [Required]
            public string Province { get; set; }

            public string Description { get; set; }

            [Display(Name = "Banner Image")]
            public string BannerImageUrl { get; set; }

            
            public List<EventCategoryViewModel> EventCategories { get; set; } = new List<EventCategoryViewModel>();
        }
    }
using System.ComponentModel.DataAnnotations;

namespace RaceDayMVC.Models
{
    public class LoginViewModel
    {
        
            [Required]
            [EmailAddress]
            public string Email { get; set; }

            [Required]
            [DataType(DataType.Password)]
            public string Password { get; set; }

            [Required]
            public string Role { get; set; } 
        }
    }
    namespace RaceDayMVC.Models
{
    public class ResultViewModel
    {
      
            public string EventName { get; set; }
            public DateTime EventDate { get; set; }
            public string Category { get; set; }

           
            public string TimeFormatted { get; set; }

            public int Position { get; set; }

            
            public int TotalFinishers { get; set; }
        }
    }
    using System.ComponentModel.DataAnnotations;

namespace RaceDayMVC.Models
{
    public class UserViewModel
    {
      
            public int UserId { get; set; }

            [Required]
            [Display(Name = "Full Name")]
            public string FullName { get; set; }

            [Required]
            [EmailAddress]
            public string Email { get; set; }

            [Required]
            public string Phone { get; set; }

            [Display(Name = "Date of Birth")]
            [DataType(DataType.Date)]
            public DateTime DateOfBirth { get; set; }

            [Display(Name = "Emergency Contact")]
            public string EmergencyContact { get; set; }

            [Display(Name = "Emergency Phone")]
            public string EmergencyPhone { get; set; }

            
            public string? ProfilePicUrl { get; set; }
        }
    }


using System.ComponentModel.DataAnnotations;

namespace RaceDayMVC.Models
{
    public class UserViewModel
    {
      
            public int UserId { get; set; }

            [Required]
            [Display(Name = "Full Name")]
            public string FullName { get; set; }

            [Required]
            [EmailAddress]
            public string Email { get; set; }

            [Required]
            public string Phone { get; set; }

            [Display(Name = "Date of Birth")]
            [DataType(DataType.Date)]
            public DateTime DateOfBirth { get; set; }

            [Display(Name = "Emergency Contact")]
            public string EmergencyContact { get; set; }

            [Display(Name = "Emergency Phone")]
            public string EmergencyPhone { get; set; }

            
            public string? ProfilePicUrl { get; set; }
        }
    }
namespace RaceDayMVC.MVC_controllers
{
    public class EnrollViewModel
    {
        public int EntryId { get; set; }
        public int RaceNumber { get; set; }


        public string EventName { get; set; }
        public DateTime EventDate { get; set; }


        public string CategoryName { get; set; }


        public string UserFullName { get; set; }
        public string UserEmail { get; set; }


        public string PaymentStatus { get; set; }
        public string EntryStatus { get; set; }
        public object EventCategoryId { get; internal set; }
    }
}
using Microsoft.AspNetCore.Mvc;
using RaceDayMVC.Models;
using RaceDayMVC.SERVICES;

namespace RaceDayMVC.MVC_controllers
{
    public class OrganiserController : Controller
    {
       
            private readonly ApiService _apiService;

            public OrganiserController(ApiService apiService)
            {
                _apiService = apiService;
            }

           
            public async Task<IActionResult> Dashboard()
            {
                if (HttpContext.Session.GetString("UserRole") != "Organiser") return RedirectToAction("Login", "Account");

                var events = await _apiService.GetAsync<List<EventViewModel>>("api/events");
                return View(events);
            }

            
            [HttpGet]
            public IActionResult CreateEvent() => View();

            [HttpPost]
            public async Task<IActionResult> CreateEvent(EventViewModel model, IFormFile bannerImage)
            {
                
                model.BannerImageUrl = "https://via.placeholder.com/800x400";

                var response = await _apiService.PostAsync("api/events", model);
                if (response.IsSuccessStatusCode) return RedirectToAction("Dashboard");

                return View(model);
            }

          
            public async Task<IActionResult> Enrollments(int eventId)
            {
                var enrollments = await _apiService.GetAsync<List<EnrollmentViewModel>>($"api/enrollments/event/{eventId}");
                return View(enrollments);
            }

           
            [HttpPost]
            public async Task<IActionResult> CaptureResult(int entryId, int timeSeconds, int position)
            {
                var resultData = new { EntryId = entryId, TimeSeconds = timeSeconds, Position = position };
                await _apiService.PostAsync("api/results/capture", resultData);
                return RedirectToAction("Enrollments");
            }
        }
    }

   using Microsoft.AspNetCore.Mvc;
using RaceDayMVC.Models;
using RaceDayMVC.SERVICES;

namespace RaceDayMVC.MVC_controllers
{
 
        public class ParticipantController : Controller
        {
            private readonly ApiService _apiService;

            public ParticipantController(ApiService apiService)
            {
                _apiService = apiService;
            }

           
            private bool IsParticipant() => HttpContext.Session.GetString("UserRole") == "Participant";

           
            public async Task<IActionResult> Index(string searchString)
            {
                var events = await _apiService.GetAsync<List<EventViewModel>>("api/events");

                if (!string.IsNullOrEmpty(searchString))
                {
                    events = events?.Where(e => e.EventName.Contains(searchString, StringComparison.OrdinalIgnoreCase)).ToList();
                }

                return View(events ?? new List<EventViewModel>());
            }

            
            public async Task<IActionResult> EventDetails(int id)
            {
                if (!IsParticipant()) return RedirectToAction("Login", "Account");

                var eventDetails = await _apiService.GetAsync<EventViewModel>($"api/events/{id}");
                if (eventDetails == null) return NotFound();

                return View(eventDetails);
            }

           
            [HttpPost]
            public async Task<IActionResult> Enroll(EnrollViewModel model)
            {
                if (!IsParticipant()) return RedirectToAction("Login", "Account");

                var response = await _apiService.PostAsync("api/enrollments", model);

                if (response.IsSuccessStatusCode)
                {
                    TempData["Success"] = "Successfully enrolled in the event!";
                    return RedirectToAction("MyEnrollments");
                }

                TempData["Error"] = "Failed to enroll. Category may be full.";
                return RedirectToAction("EventDetails", new { id = model.EventCategoryId }); 
            }

            
            public async Task<IActionResult> MyEnrollments()
            {
                if (!IsParticipant()) return RedirectToAction("Login", "Account");

                var myEntries = await _apiService.GetAsync<List<EnrollmentViewModel>>("api/enrollments/my-entries");
                return View(myEntries ?? new List<EnrollmentViewModel>());
            }

            
            public async Task<IActionResult> MyResults()
            {
                if (!IsParticipant()) return RedirectToAction("Login", "Account");

                var results = await _apiService.GetAsync<List<ResultViewModel>>("api/results/my-results");
                return View(results ?? new List<ResultViewModel>());
            }

           
            [HttpGet]
            public async Task<IActionResult> Profile()
            {
                if (!IsParticipant()) return RedirectToAction("Login", "Account");

                var userProfile = await _apiService.GetAsync<UserViewModel>("api/users/profile");
                if (userProfile == null) return NotFound();

                return View(userProfile);
            }

           
            [HttpPost]
            public async Task<IActionResult> Profile(UserViewModel model)
            {
                if (!IsParticipant()) return RedirectToAction("Login", "Account");

                if (!ModelState.IsValid) return View(model);

                var response = await _apiService.PutAsync("api/users/profile", model);

                if (response.IsSuccessStatusCode)
                {
                    TempData["Success"] = "Profile updated successfully.";
                    return RedirectToAction("Profile");
                }

                TempData["Error"] = "Failed to update profile.";
                return View(model);
            }
        }
    }
    using Microsoft.AspNetCore.Mvc;
using RaceDayMVC.SERVICES;

namespace RaceDayMVC.MVC_controllers
{
    public class RaceDayMVCController : Controller
    {
        private static ApiService apiService;
        private readonly ApiService _apiService = apiService;

            [HttpGet]
            public IActionResult Login() => View();

            [HttpPost]
            public async Task<IActionResult> Login(string email, string password, string role)
            {
                var loginData = new { Email = email, Password = password, Role = role };
                var response = await _apiService.PostAsync("api/auth/login", loginData);

                if (response.IsSuccessStatusCode)
                {

                    if (response.Headers.TryGetValues("Set-Cookie", out var cookies))
                    {
                        HttpContext.Session.SetString("ApiAuthCookie", cookies.FirstOrDefault() ?? "");
                    }


                    HttpContext.Session.SetString("UserRole", role);
                    HttpContext.Session.SetString("UserEmail", email);

                    if (role == "Organiser") return RedirectToAction("Dashboard", "Organiser");
                    return RedirectToAction("Index", "Participant");
                }

                ViewBag.Error = "Invalid login attempt.";
                return View();
            }

            public IActionResult Logout()
            {
                HttpContext.Session.Clear();
                return RedirectToAction("Index", "Home");
            }
        }
}
       
       
      
      


    


    
            
            public string PaymentStatus { get; set; }
            public string EntryStatus { get; set; }
        }
    }
using System.Text;
using System.Text.Json;

namespace RaceDayMVC.SERVICES
{
    public class ApiService
    {
        private readonly HttpClient _httpClient;
        private readonly IHttpContextAccessor _httpContextAccessor;

        public ApiService(HttpClient httpClient, IHttpContextAccessor httpContextAccessor)
        {
            _httpClient = httpClient;
            _httpContextAccessor = httpContextAccessor;

            
            _httpClient.BaseAddress = new Uri("https://localhost:7123/");
        }

        
        private void AttachAuthCookie()
        {
            var sessionCookie = _httpContextAccessor.HttpContext?.Session.GetString("ApiAuthCookie");
            if (!string.IsNullOrEmpty(sessionCookie))
            {
                _httpClient.DefaultRequestHeaders.Add("Cookie", sessionCookie);
            }
        }

       
        public async Task<T> GetAsync<T>(string endpoint)
        {
            AttachAuthCookie();
            var response = await _httpClient.GetAsync(endpoint);
            if (response.IsSuccessStatusCode)
            {
                var json = await response.Content.ReadAsStringAsync();
                return JsonSerializer.Deserialize<T>(json, new JsonSerializerOptions { PropertyNameCaseInsensitive = true });
            }
            return default;
        }

        
        public async Task<HttpResponseMessage> PostAsync<T>(string endpoint, T data)
        {
            AttachAuthCookie();
            var json = JsonSerializer.Serialize(data);
            var content = new StringContent(json, Encoding.UTF8, "application/json");
            return await _httpClient.PostAsync(endpoint, content);
        }

        
        public async Task<HttpResponseMessage> PutAsync<T>(string endpoint, T data)
        {
            AttachAuthCookie();
            var json = JsonSerializer.Serialize(data);
            var content = new StringContent(json, Encoding.UTF8, "application/json");
            return await _httpClient.PutAsync(endpoint, content);
        }
    }
}
using RaceDayMVC.SERVICES;

var builder = WebApplication.CreateBuilder(args);


builder.Services.AddControllersWithViews();
builder.Services.AddHttpContextAccessor();
builder.Services.AddHttpClient<ApiService>();
builder.Services.AddSession(options => {
    options.IdleTimeout = TimeSpan.FromMinutes(30);
    options.Cookie.HttpOnly = true;
    options.Cookie.IsEssential = true;
});

var app = builder.Build();

app.UseStaticFiles();
app.UseRouting();
app.UseSession();

app.MapControllerRoute(
    name: "default",
    pattern: "{controller=Home}/{action=Index}/{id?}");

app.Run();




